Title: Use boxcar to send nagios alerts
Date: 2013-07-28
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/use-boxcar-to-send-nagios-alerts/
disqus_identifier: 777 http://www.linuxsysadmintutorials.com/?p=777
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Use boxcar to send nagios alerts

Traditionally, Operations Teams setup nagios to send alerts using SMS. Whilst SMS is out-of-band and would withstand outages to your IP links, in some cases it becomes cost prohibitive especially for small startups with little cash to burn. 

Using push notifications over IP to smart phones is an alternative which in most cases is free though does result in your monitoring servers ability to notify your operations teams of critical issues relying on your IP link being up. 

One push notification service is <a href="http://boxcar.io/">boxcar.io</a>.

Sign up with <a href="http://boxcar.io/">boxcar.io</a> and install the app on your iOS device.

Drop the small boxcar shell script on the nagios server. This script is a copy and paste of the script <a href="<a href="http://jedda.me/2012/05/boxcar-push-nagios-notifications/">posted</a> by Jedda Wignall.

```null
#!/bin/bash
while getopts "e:h:m:" optionName; do
  case "$optionName" in
    e) boxcarEmail=( "$OPTARG" );;
    h) host=( "$OPTARG" );;
    m) message=( "$OPTARG" );;
  esac
done

curl --sslv3 --data-urlencode "email=$boxcarEmail" \
  --data-urlencode "&notification[from_screen_name]=$host" \
  --data-urlencode "&notification[message]=$message" https://boxcar.io/devices/providers/MH0S7xOFSwVLNvNhTpiC/notifications
```

You will need to **subscribe** to the boxcapp Monitoring generic provider.

```
$ curl -d "email=your-boxcar-registered-email@example.com" http://boxcar.io/devices/providers/MH0S7xOFSwVLNvNhTpiC/notifications/subscribe
```

Create the nagios host and service command objects that nagios will call when it needs to send an alert.

```
define command {
  command_name  notify-host-by-boxcar
  command_line /bin/bash /usr/local/bin/boxcar.sh -e "$CONTACTADDRESS1$" -h "$HOSTNAME$" -m "$NOTIFICATIONTYPE$: $HOSTSTATE$"
}

define command {
  command_name  notify-service-by-boxcar
  command_line /bin/bash /usr/local/bin/boxcar.sh -e "$CONTACTADDRESS1$" -h "$HOSTNAME$" -m "$NOTIFICATIONTYPE$: $SERVICEDESC$ $SERVICESTATE$ ($SERVICEOUTPUT$)"
}
```


Finally you will then need to update your nagios contact object adding an <strong>address1</strong> definition.

```
define contact{
  contact_name                    rene
  use                             generic-contact
  alias                           Rene Cunningham
  service_notification_commands   notify-service-by-email,notify-service-by-boxcar
  host_notification_commands      notify-host-by-email,notify-host-by-boxcar
  email                           rene@linuxsysadmintutorials.com
  address1                        rene@example.com
}
```


Restart the nagios service. The next nagios host or service alert will be sent by boxcar.

```
# service nagios restart
```

<h3>Linux Sysadmin Protip</h3>
You can quickly test whether boxcar notifications are working with nagios by generating a failed service or host with iptables. Choose a host or a service that nagios is monitoring and use iptables to block outbound connections.

For example, if nagios was monitoring HTTP on web-server-1.example.com block all outbound packets from the monitoring server to web-server-1.exmaple.com on port 80.

```
# iptables -I OUTPUT -d web-server-1.example.com -p tcp --dport 80 -j REJECT
```

Log into the nagios web interface and <strong>Re-schedule the next check of this service</strong> for this particular service. Delete the iptables rule once you've finished testing.

```
# iptables -D OUTPUT -d web-server-1.example.com -p tcp --dport 80 -j REJECT
```

<p style="font-size: xx-small;">all code and configs can be found on <a href="https://github.com/rene00/linuxsysadmintutorials.com/tree/master/boxcar-nagios">github.com/rene00/linuxsysadmintutorials.com/tree/master/boxcar-nagios</a></p>
