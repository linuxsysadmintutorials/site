Title: Transfer a text file over a TCP socket using perl
Date: 2012-10-30
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/transfer-a-text-file-over-a-tcp-socket-using-perl/
disqus_identifier: 507 http://www.linuxsysadmintutorials.com/?p=507
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Transfer a text file over a TCP socket using perl

Ever needed to transfer the contents of a text file over a TCP socket and didnt have tools like nc, scp or rsync?

By crunching enough perl together, a one-liner will do it.

Anyone who needs to view the contents of /etc/passwd now only needs to connect to port 1234/tcp.

```
$ perl -MIO::Socket::INET -ne 'BEGIN{$l=IO::Socket::INET->new(
  LocalPort=>1234,Proto=>"tcp",Listen=>5,ReuseAddr=>1);
  $l=$l->accept}print $l $_' < /etc/passwd
```
