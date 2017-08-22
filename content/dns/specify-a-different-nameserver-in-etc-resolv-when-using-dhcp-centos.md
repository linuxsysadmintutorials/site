Title: Specify a different nameserver in /etc/resolv.conf when using DHCP in CentOS
Date: 2014-09-24
disqus_url: http://www.linuxsysadmintutorials.com/specify-a-different-nameserver-in-etcresolv-conf-when-using-dhcp-in-centosrhelfedora/


When using dhcp within a ifcfg network configuration file you may want to specify a different nameserver instead of using the nameserver assigned by the DHCP server.

Use the PEERDNS, DNS1 and DNS2 options.

```
DEVICE="em1"
BOOTPROTO="dhcp"
ONBOOT="yes"
TYPE="Ethernet"
PEERDNS="yes"
DNS1="8.8.8.8"
DNS2="8.8.4.4"
```

To check that it all works.

```
# ifdown eth0;ifup eth0
# grep ^nameserver /etc/resolv.conf
nameserver 8.8.8.8
nameserver 8.8.4.4
```

If you want to use the nameservers that are provided by DHCP server but also specify an extra nameserver within your /etc/resolv.conf, you can make use of dhclient.conf options in /etc/dhcp/dhclient-em1.cfg

```
:::bash
prepend domain-name-servers 8.8.4.4;
```

