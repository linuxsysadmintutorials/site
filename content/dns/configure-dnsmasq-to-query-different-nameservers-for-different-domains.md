Title: Configure dnsmasq to query different nameservers for different domains
Date: 2015-02-12
disqus_url: http://www.linuxsysadmintutorials.com/configure-dnsmasq-to-query-different-nameservers-for-different-domains/

When connecting to a VPN you may have a DNS server which serves [split horizon](https://en.wikipedia.org/wiki/Split-horizon_DNS) for a particular domain. For
example when connected to your companies VPN, your local DNS config in
/etc/resolv.conf is updated with:

```
:::bash
nameserver 192.168.1.1
```

The DNS server 192.168.1.1 is your companies internal DNS server which resolves admin.example.org to 192.168.1.100. You need to access admin.example.org on 192.168.1.100 but don’t necessarily want to have all DNS queries go to 192.168.1.1. You also don’t want manage /etc/hosts entries which can become stale over time.

[dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) a lightweight DNS and DHCP service can help.

Simply install dnsmasq, starting off with a simple config.

```
:::bash
listen-address=127.0.0.1
bind-interfaces
conf-dir=/etc/dnsmasq.d/
```

Create /etc/dnsmasq.d/example.org.conf.

```
:::bash
address=/vpn.example.org/198.51.100.100
server=/example.org/192.168.1.1
```

 - line 1 returns 198.51.100.100 for the host vpn.example.org.
 - line 2 specifies 192.168.1.1 as the upstream DNS server for all other example.org queries such as admin.example.org.

Reload the dnsmasq service.

```
service dnsmasq restart
```

And finally update /etc/resolv.conf.

```
:::bash
nameserver 127.0.0.1
```

Now your local resolver clients will use dnsmasq as a DNS server with dnsmasq only forwarding queries for example.org to the upstream DNS server 192.168.1.1.
