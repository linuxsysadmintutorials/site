Title: Handle apt keys with puppet
Date: 2011-08-14
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/handle-apt-keys-with-puppet/
disqus_identifier: 92 http://www.linuxsysadmintutorials.com/?p=92
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Handle apt keys with puppet

Clone the fork of [puppet-apt](https://github.com/ctrlaltdel/puppet-apt) module
by [camptocamp](https://github.com/camptocamp/).

```
$ git clone https://github.com/ctrlaltdel/puppet-apt.git
```

Move this into the puppet modules directory.

```
$ mv puppet-apt apt
$ sudo rsync -avP --exclude=.git apt /etc/puppet/modules/
```

Within the nodes puppet manifest

```
include apt

apt::key { "3EBC26B60C5A2783":
    ensure => present,
}
```

If you know where the public key file is hosted you can use the source
attribute with apt::key

```
include apt

apt::key { "google chrome":
    ensure => present,
    source => 'https://dl-ssl.google.com/linux/linux_signing_key.pub',
}
```
