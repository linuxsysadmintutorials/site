Title: Setup Nagios NRPE to monitor system load on CentOS
Date: 2013-09-12
disqus_url: http://www.linuxsysadmintutorials.com/setup-a-nagios-nrpe-to-monitor-the-load-on-a-system/

Nagios Remote Plugin Executor (NRPE), is used by nagios to run commands
remotely on linux servers.

For example to discover the system load which is a measure of computional
work, on a linux server you would run the uptime command which provides
you with the load average.

```
# uptime
 22:41:02 up 14 min,  1 user,  load average: 0.00, 1.02, 2.40
```

Without NRPE, nagios is unable to run this command remotely.

NRPE works by running as a daemon on the remote linux server listening on a
port. Nagios connects to the NRPE daemon and runs a NRPE command which
typically calls a nagios plugin such as check_load, returning some info
aswell as a status of OK, WARNING, CRITICAL or UNKNOWN.

First, install and start the NRPE server on the remote linux server.

```
# yum install nrpe
# service nrpe start
```

NRPE by default listens on port 5666/tcp which you will need to allow 
through if your remote linux server is running a firewall. Be sure to
only expose the NRPE service to your nagios server by specifying the
IP address of your nagios server with -s in your iptables rules.

**/etc/sysconfig/iptables**

```
-A INPUT -m state --state NEW -m tcp -p tcp --dport 5666 -s 10.0.0.1 -j ACCEPT
```

Then restart the firewall.

```
# service iptables restart
```

On the nagios server, install the check_nrpe plugin.

```
# yum install nagios-plugins-nrpe
```

check_nrpe is the executable that nagios will use to query the remote
linux server.

```
# /usr/lib64/nagios/plugins/check_nrpe -H 10.0.1.254
CHECK_NRPE: Error - Could not complete SSL handshake.
```

NRPE will only allow certain hosts to connect which is configured by the
allowed_hosts option in /etc/nagios/nrpe.cfg. Let's add our nagios
server and restart the NRPE service.

**/etc/nagios/nrpe.cfg**

```
allowed_hosts=127.0.0.1,10.0.0.1
```

```
# service nrpe restart
```

Again, let's run check_nrpe from our nagios server.

```
# /usr/lib64/nagios/plugins/check_nrpe -H 10.0.1.254
NRPE v2.14
```

By default, NRPE should be configured with a check_load command.

```
command[check_load]=/usr/lib64/nagios/plugins/check_load -w 15,10,5 -c 30,25,20
```

Call this command from the nagios server via NRPE using the check_nrpe plugin.


```text
# /usr/lib64/nagios/plugins/check_nrpe -H 10.0.1.254 -c check_load
OK - load average: 0.00, 0.00, 0.00|load1=0.000;15.000;30.000;0; load5=0.000;10.000;25.000;0; load15=0.000;5.000;20.000;0;
```

Once we've confirmed that the nagios server is able to communicate with the
remote linux server over NRPE we can configure nagios.

**/etc/nagios/conf.d/service_nrpe-load-web-server.cfg**

```
define service {
  use                            generic-service
  host_name                      webserver.example.org
  service_description            NRPE - Load
  check_command                  check_nrpe!check_load
}
```

And then finally reload nagios.

```
# service nagios reload
```
