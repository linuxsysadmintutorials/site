Title: Delete lease from dnsmasq
Status: Published
Date: 2016-05-28

dnsmasq keeps track of the DHCP leases it has in a file which is defined
by the dhcp-leasefile config option.

To delete a lease from dnsmasq, first stop dnsmasq.

```
$ sudo systemctl stop dnsmasq
```

Remove the lease from the file. On Ubuntu this lease file defaults to
/var/lib/misc/dnsmasq.leases.

Once the lease is removed, start dnsmasq.

```
$ sudo systemctl start dnsmasq
```
