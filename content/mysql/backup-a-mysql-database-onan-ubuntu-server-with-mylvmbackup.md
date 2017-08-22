Title: Backup a MySQL database on an Ubuntu server with mylvmbackup.
Date: 2012-01-01
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/backup-a-mysql-database-on-a-ubuntu-server-with-mylvmbackup/
disqus_identifier: 363 http://www.linuxsysadmintutorials.com/?p=363
disqus_shortname: wwwlinuxsysadmintutorials'
disqus_title: Backup a MySQL database on an Ubuntu server with mylvmbackup

[mylvmbackup](http://www.lenzg.net/mylvmbackup) is a zero downtime backup 
solution for MySQL databases. It uses a global read lock to quiesce the 
MySQL database and snapshots the MySQL directory using LVM. Because it acts 
at the filesystem layer taking advantage of LVM snapshots, it is agnostic 
when it comes to MySQL server versions and storage engines.

The end result is a complete snapshot of the MySQL directory which can simply 
be imported onto any other server running a simliar version of MySQL.

## Lets get started!

First confirm that your MySQL data directory is on a LVM logical volume.

```
$ df -h /var/lib/mysql
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/system-var
                       20G  7.8G   11G  42% /var
$ sudo lvdisplay /dev/mapper/system-var
  --- Logical volume ---
  LV Name                /dev/system/var
  VG Name                system
  LV UUID                d4o3fB-WYEd-FHUP-J41u-NkP8-DP90-omIG0n
  LV Write Access        read/write
  LV Status              available
  # open                 1
  LV Size                20.00 GiB
  Current LE             5120
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1
```

The MySQL data directory needs to sit on an LVM logical volume as 
mylvmbackup will create a snapshot of this volume. The logical volume that 
will be used for this tutorial is /dev/system/var.

Furthermore, we need to have sufficient free space on the volume group that
the volume sits in. /dev/system/var sits in the system volume group.

```
$ sudo vgdisplay -s system 
  "system" 926.84 GiB [896.56 GiB used / 30.27 GiB free]
```

## How much space do you need free?

It depends on how many disk writes the volume group is doing as LVM will be
writing the new blocks to the undo space. Use iostat to determine how much
your disks are writing and divide this figure by 2. Check out 
[MySQL Performan Blog's](http://www.mysqlperformanceblog.com) article on 
[estimating undo space needed for lvm snapshot](http://www.mysqlperformanceblog.com/2008/06/09/estimating-undo-space-needed-for-lvm-snapshot).

If you don't have any free logical extents in your volume group, I suggest 
adding another disk and extending your volume group.

## Install and configure mylvmbackup

mylvmbackup 0.13 ships with oneiric.

```
$ sudo apt-get install mylvmbackup
```

Add a MySQL account which mylvmbackup will use to enforce a global read lock.

```
mysql> CREATE USER 'mylvmbackup'@'localhost' IDENTIFIED BY 'COMPLEX_PASSW0RD'; 
mysql> GRANT RELOAD, REPLICATION CLIENT ON *.* TO 'mylvmbackup'@'localhost'; 
mysql> GRANT SELECT ON mysql.* TO 'mylvmbackup'@'localhost'; 
mysql> FLUSH PRIVILEGES; 
```

Update /etc/mylvmbackup.conf with the new MySQL account. Other options you 
will need to consider are:

 - vgname - the LVM volume group name which in this tutorials case is system.
 - lvname - the LVM logical volume name which is var in this tutorial.
 - lvsize - the size of the LVM snapshot.
 - xfs - if the logical volume has been formatted using the XFS file system set
   xfs to 1.
 - mountdir - the directory where the LVM snapshot will be mounted. Ensure that
   this directory has been excluded from any other backup system like rsnapshot.
   If you're after a recommendation, I would use a directory buried within 
   /var/cache which is in the
   [Linux FHS](http://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/var.html).
 - backupdir - the directory where mylvmbackup will drop its tarball. I'd
   recommend not storing this on your root filesystem and potentially on a
   different disk controller to your MySQL database filesystem. Also ensure
   that you have some sort of rotation mechanism in place to ship or rm older
   snapshots off the filesystem so this directory doesnt end up filling up
   your filesystem. Again, base the directory of the Linux FHS.
 - relpath - the relative path of the MySQL data directory from the root of
   your logical volume. In this example, /var was the root of the logical
   volume so I had to use the relpath option of lib/mysql.

If you're happy to go with what I recommend, use the command below.

```
# cat - <<EOF /etc/mylvmbackup.conf 
[mysql] 
user=mylvmbackup 
password=COMPLEX_PASSW0RD 
host=localhost 
port= 
socket= 
mycnf=/etc/mysql/my.cnf

[lvm]
vgname=system
lvname=var
backuplv=
lvsize=2G

[fs]
xfs=0
mountdir=/var/cache/mylvmbackup/mnt/
backupdir=/var/cache/mylvmbackup/backup/
relpath=lib/mysql

[tools]
lvcreate=lvcreate
lvremove=lvremove
lvs=lvs
mount=mount
tar=tar
compress=gzip
rsync=rsync
rsnap=rsnap
umount=umount

[misc]
backuptype=tar
prefix=backup
suffix=_mysql
tararg=cvf
tarsuffixarg=
tarfilesuffix=.tar.gz
compressarg=--stdout --verbose --best
rsyncarg=-avWP
rsnaparg=7
datefmt=%Y%m%d_%H%M%S
innodb_recover=0
pidfile=/var/run/mylvmbackup_recoverserver.pid
skip_flush_tables=0
extra_flush_tables=0
skip_mycnf=0
hooksdir=/usr/share/mylvmbackup
skip_hooks=0
keep_snapshot=0
keep_mount=0
quiet=0

[logging]
log_method=console
syslog_socktype=native
syslog_facility=
syslog_remotehost=
EOF
```

You should now be in a position to take the first mylvmbackup snapshot.

```
$ sudo mylvmbackup
```

## Post Installation Tasks

Run mylvmbackup routinely through cron to take snapshots of your MySQL servers.

```
# cat - <<EOF > /etc/cron.d/mylvmbackup
PATH=/sbin:/usr/sbin:/bin:/usr/bin:/usr/local/sbin:/usr/local/bin
10 23 * * * root test -x /usr/bin/mylvmbackup && /usr/bin/mylvmbackup
EOF
```

Also ensure that older tarballs are removed from your server as you don't
want to run the risk of filling up your filesystem.

```
# cat - <<EOF > /etc/cron.d/mylvmbackup-cleanup
# remove mylvmbackup snapshots older than 30 days
15 01 * * * root find /var/cache/mylvmbackup/backup/ -type f -name 'backup-*_mysql.tar.gz' -mtime +30 -exec rm -f {} \;
EOF
```
