Title: Disable UNIStim in asterisk
Date: 2012-06-21
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/disable-unistim-in-asterisk/
disqus_identifier: 478 http://www.linuxsysadmintutorials.com/?p=478
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Disable UNIStim in asterisk

You may have noticed that asterisk has <a href="http://en.wikipedia.org/wiki/UNIStim">UNIStim</a> enabled by default which listens on port 5000.

```
$ sudo netstat -tapun | grep asterisk
...
udp        0      0 0.0.0.0:5000            0.0.0.0:* 19470/asterisk  
udp        0      0 0.0.0.0:5060            0.0.0.0:* 19470/asterisk  
udp        0      0 0.0.0.0:4569            0.0.0.0:* 19470/asterisk  
```

To disable unistim you will need to blacklist the unistim module by adding it to /etc/asterisk/modules.conf under the modules section.

```
[modules]
noload => chan_unistim.so
```

Once added, reload asterisk and asterisk will no longer be listening on port 5000.

```
$ sudo service asterisk reload
```
