Title: Using SIP over TCP with asterisk
Date: 2014-09-20
disqus_url: http://www.linuxsysadmintutorials.com/using-sip-over-tcp-with-asterisk/

SIP commonly runs over UDP but there are times when you may need to run it
over TCP. 

To allow SIP TCP clients to connect with 
<a href="http://www.asterisk.org">asterisk</a> update /etc/asterisk/sip.conf.

**/etc/asterisk/sip.conf**

```
[general]
tcpenable=yes
tcpbindaddr=0.0.0.0
```

Within your SIP clients definition you have to add transport=tcp for
each individual connection.

```
[client001]
callerid="Client 001" <001>
username=client001
secret=password
type=friend
host=dynamic
context=internal
canreinvite=yes
mailbox=001@default
transport=tcp
disallow=all
allow=alaw
nat=route
dtmfmode=inband
```

Reload sip within the asterisk console and confirm that asterisk is now
listening on 5060/tcp with netstat.

```
server*CLI> sip reload
server*CLI> quit
Executing last minute cleanups
$ sudo netstat  -tlpn | grep 5060
tcp        0      0 0.0.0.0:5060            0.0.0.0:*               LISTEN      17414/asterisk  
```
