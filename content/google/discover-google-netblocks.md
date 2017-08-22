Title: Discover Googles Netblocks
Date: 2014-09-19
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/discover-googles-netblocks/
disqus_identifier: 925 http://www.linuxsysadmintutorials.com/?p=925
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Discover Googles Netblocks

Do you need to discover what IP netblocks are owned and operated by
google to perhaps add to your firewall ACLs? 

Use <a href="http://www.oreillynet.com/linux/cmd/cmd.csp?path=d/dig">dig</a>.

```
$ dig -t txt _netblocks.google.com
```
