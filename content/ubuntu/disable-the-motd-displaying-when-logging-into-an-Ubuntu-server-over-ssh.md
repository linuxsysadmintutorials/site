Title: Disable the MOTD from displaying when logging into an Ubuntu server over SSH
Date: 2014-09-14
Status: Published

Ever logged into an Ubuntu box and seen this?

```
Welcome to Ubuntu 14.04.1 LTS (GNU/Linux 3.13.0-29-generic x86_64)
 
 * Documentation:  https://help.ubuntu.com/
 
  System information as of Sat Sep 13 23:12:33 UTC 2014
 
  System load:  0.08              Processes:           118
  Usage of /:   12.7% of 7.74GB   Users logged in:     1
  Memory usage: 16%               IP address for eth0: 10.1.1.1
  Swap usage:   0%
 
  Graph this data and manage this system at:
    https://landscape.canonical.com/
 
  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud
```

You can configure /etc/pam.d/sshd to not include the pam_motd.so module when logging in. Just hash out the following lines in /etc/pam.d/sshd.

```
session    optional     pam_motd.so  motd=/run/motd.dynamic noupdate
session    optional     pam_motd.so # [1]
```
