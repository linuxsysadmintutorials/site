Title: how do I sniff each packet on the command line?
Date: 2011-09-17
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/how-do-i-sniff-each-packet-on-the-command-line/
disqus_identifier: 292 http://www.linuxsysadmintutorials.com/how-do-i-sniff-each-packet-on-the-command-line/
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: how do I sniff each packet on the command line?

<p>tcpdump is a console based traffic packet capture tool. By using
tcpdump with -A and -s we can print each packet in ASCII.</p>

```
$ sudo tcpdump -i any -s 0 -A
```
