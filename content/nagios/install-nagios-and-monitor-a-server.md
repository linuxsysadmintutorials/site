Title: Install Nagios on CentOS and monitor a server
Date: 2013-08-05
disqus_url: http://www.linuxsysadmintutorials.com/instal-nagios-and-monitor-a-server/

Nagios is a powerful open source monitoring system that is used by many IT
organisations to monitor IT infrastructure. Without a doubt, Nagios is the 
most widely used monitoring application that Linux System Administrators
deploy today.

There are some configuration objects that nagios uses which we will cover 
today. These objects are contacts, hosts, services and commands.

To install and setup your first server within nagios, start by enabling 
the <a href="/enable-epel-on-a-centosrhel-6-server">EPEL</a> repository 
if you haven't already.

Install the nagios RPM and it's ping plugin.

```
# yum install nagios nagios-plugins-icmp
```

This will install the nagios web interface which you can access at 
http://server/nagios which has a username and password of 
<strong>nagiosadmin</strong>. There is still some further configuration that
needs to be done before any servers show up within the web interface.

Nagios contacts are used to send notifications of alerts and recoveries.
Create the first nagios contact in the file /etc/nagios/conf.d/contacts.cfg

```
define contact {
  contact_name    john
  alias           John Example (sysadmin)
  use             generic-contact
  email           john@exmaple.com
}
```

Nagios hosts define servers, routers, switches and other devices which 
nagios monitors. Create the first nagios host in the file 
/etc/nagios/conf.d/hosts.cfg

```
define host {
  use                   generic-host
  host_name             server.example.com
  address               server.example.com
  max_check_attempts    5
}
```

Nagios services objects are services that nagios monitors on the remote server.
Create the first nagios service in the file /etc/nagios/conf.d/services.cfg

```
define service {
  use                   generic-service
  host_name             server.example.com
  service_description   PING
  check_command         check_icmp!500.0,80%!1000.0,100%
}
```

Nagios service objects call nagios commands which are a defined set of 
objects that run commands which return OK, WARNING, CRITICAL or UNKNOWN.
The first Nagios command that we will setup is a simple ping command.

```
define command{
  command_name    check_icmp
  command_line    $USER1$/check_icmp -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$
}
```

Restart the nagios service.

```
# service nagios restart
```

Bring up the nagios web interface again and go to "Services" where you will
find the server you are now monitoring. It will take up to 5 minutes for
nagios to run the service check "PING" which hopefully returns an OK.

### Linux Sysadmin Protip

You can force Nagios to execute a service check and not have to wait 5 
minutes by going to the "Service Information" page for a particular 
service and choosing "Re-schedule the next check for this service".
