Title: Install puppetlabs apt puppet module
Date: 2012-02-19
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/install-puppetlabs-apt-puppet-module/
disqus_identifier: 391 http://www.linuxsysadmintutorials.com/?p=391
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Install puppetlabs apt puppet module

<a href="http://puppetlabs.com/">Puppetlabs</a> have an <a
href="https://github.com/puppetlabs/puppet-apt">apt puppet module</a>
which provides helpful definitions to handle Apt configuration. You will
find that the puppetlabs modules use these definitions. 

Without this module, you will run into puppet invalid resource type
errors

```
err: Could not retrieve catalog from remote server: Error 400 on SERVER:
Puppet::Parser::AST::Resource failed with error ArgumentError: Invalid resource type apt::source at /etc/puppet/modules/rabbitmq/manifests/repo/apt.pp:15 on node server.example.com
```

To install the puppetlabs puppet module use git to clone the repository
directly into the /etc/puppet/modules directory of your puppetmaster
server.

```
$ cd /etc/puppet/modules
$ git clone https://github.com/puppetlabs/puppet-apt.git
```
