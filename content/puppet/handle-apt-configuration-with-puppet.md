Title: Handle apt configuration with puppet
Date: 2011-09-01
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/handle-apt-configuration-puppet/
disqus_identifier: 95 http://www.linuxsysadmintutorials.com/?p=95
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Handle apt configuration with puppet

Clone the fork of <a href="https://github.com/vurbia/puppet-apt">puppet-apt</a> module by <a href="https://github.com/camptocamp/">camptocamp</a> on <a href="http://github.com">github</a>.

```
$ git clone https://github.com/vurbia/puppet-apt.git
```

Move this into the puppet modules directory.

```
$ mv puppet-apt apt
$ sudo rsync -avP --exclude=.git apt /etc/puppet/modules/
```

Within the nodes puppet manifest

```
include apt

apt::conf{"99unattended-upgrade":
    ensure  => present,
    content => "APT::Periodic::Unattended-Upgrade \"1\";",
}
```
