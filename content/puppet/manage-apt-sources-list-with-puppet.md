Title: Manage apt sources.list with puppet
Date: 2011-08-13
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/manage-apt-sources-list-with-puppet/
disqus_identifier: 86 http://www.linuxsysadmintutorials.com/?p=86
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Manage apt sources.list with puppet

<p>Clone the fork of <a href="https://github.com/renecunningham/puppet-apt">puppet-apt</a> module by <a href="https://github.com/camptocamp/">camptocamp</a> on <a href="http://github.com">github</a>.</p>

```
$ git clone https://github.com/renecunningham/puppet-apt
```

<p>Move this into the puppet modules directory.</p>

```
$ mv puppet-apt apt
$ sudo rsync -avP --exclude=.git apt /etc/puppet/modules/
```

<p>Within the nodes puppet manifest</p>

```
include apt

apt::sources_list {"medibuntu":
    ensure => present,
    content => "deb http://packages.medibuntu.org/ maverick free non-free"
}
```
