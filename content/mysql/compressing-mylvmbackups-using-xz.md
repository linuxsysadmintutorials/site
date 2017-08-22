Title: Compressing mylvmbackups using xz
Date: 2013-06-23
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/compressing-mylvmbackups-using-xz/
disqus_identifier: 758 http://www.linuxsysadmintutorials.com/?p=758
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Compressing mylvmbackups using xz

Switching the compression algorithm in <a href="/backup-a-mysql-database-on-an-ubuntu-server-with-mylvmbackup.html">mylvmbackup</a> is simple as changing the <strong>compress</strong>, <strong>compressarg</strong> and <strong>tarfilesuffix</strong> config options in <strong>/etc/mylvmbackup.conf</strong>

To use <a href="https://en.wikipedia.org/wiki/Xz">xz</a> as the compression algorithm, base your <strong>/etc/mylvmbackup.conf</strong> from the excerpt below.

```
[tools]
lvcreate=lvcreate
lvremove=lvremove
lvs=lvs
mount=mount
tar=tar
compress=xz
rsync=rsync
rsnap=rsnap
umount=umount

[misc]
backuptype=tar
prefix=backup
suffix=_mysql
tararg=cvf
tarsuffixarg=
tarfilesuffix=.tar.xz
compressarg=--stdout --verbose -9 --extreme
rsyncarg=-avWP
rsnaparg=7
datefmt=%Y%m%d_%H%M%S
```

From previous results, mylvmbackup backups using xz have yielded about half the size of a gzip backup. Be mindful of CPU and memory usage whilst the compression of the LVM snapshot is occurring. Also keep in mind the number of free physical extents you have for the LVM volume group as xz with the arguments above may take longer to compress over gzip.

