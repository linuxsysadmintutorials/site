Title: Create a socks5 tunnel on port 3333 over SSH
Date: 2011-09-02
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/create-a-socks5-tunnel-on-port-3333-over-ssh/
disqus_identifier: 19 http://www.linuxsysadmintutorials.com/blog/?p=19
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Create a socks5 tunnel on port 3333 over SSH

To create a a quick and dirty socks5 tunnel with SSH use the '-D' option.

```
$ ssh -D 3333 $REMOTE_HOST
```

Once setup, point your browser to localhost:3333 as a SOCKS proxy.
