Title: Configure guest domain memory usage with virsh
Date: 2013-09-06
disqus_url: http://www.linuxsysadmintutorials.com/configuring-guest-domain-memory-usage-with-virsh/

virsh provides Linux System Administrators with the ability to dynamically
scale allocated memory to virtual guests during runtime.

```
# virsh dommemstat web-server
actual 2097152
rss 903040
```

dommemstat shows that the domain web-server has a memory allocation limit
of 2097152 kB and is currently using 903040 kB. You can confirm this 
with ps.

```
# ps -C qemu-kvm -o rss,cmd
  RSS CMD
903040 /usr/bin/qemu-kvm -name web-server -S -M pc-1.2 -enable-kvm -m 2048 -smp
```

The memory allocation limit is set with setmaxmem and can only be
performed whilst the domain is inactive.

```
# virsh setmaxmem web-server 1048576 --config
```

By passing setmaxmem the config option the memory allocation limit is
written to the virsh configuration file.

The current memory allocation for a guest domain is set with setmem.

```
# virsh setmem web-server 786432 --config --live
```

When passing setmem the live option, a memory balloon is performed on the
running guest and the change happens instantly. This can be verified with
dominfo.

```
# virsh dominfo web-server
Id:             3
Name:           web-server
UUID:           a1a0ee38-20ff-144b-a0ef-2bb14e4ce167
OS Type:        hvm
State:          running
CPU(s):         2
CPU time:       33.9s
Max memory:     1048576 KiB
Used memory:    786432 KiB
Persistent:     yes
Autostart:      disable
Managed save:   no
Security model: selinux
Security DOI:   0
Security label: system_u:system_r:svirt_t:s0:c782,c903 (enforcing)
```
