Title: Create a simple puppet class to maintain sshd
Date: 2011-07-10
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/create-a-simple-puppet-class-to-maintain-sshd/
disqus_identifier: 124 http://www.linuxsysadmintutorials.com/?p=124
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Create a simple puppet class to maintain sshd

## The Manifest

Create the directories we need for the puppet module.

```
$ sudo mkdir -p /etc/puppet/modules/sshd/{manifests,files}
```

First, lets create the sshd class file which lives in 
/etc/puppet/modules/sshd/manifests/init.pp.

```
class sshd {

    package { 'openssh-server':
        ensure => latest
    }

    service { 'ssh':
        subscribe => File[sshdconfig],
        require => Package['openssh-server'],
    }

    file { 'sshdconfig':
        name => '/etc/ssh/sshd_config',
        owner => root,
        group => root,
        mode => 644,
        source => 'puppet:///modules/sshd/sshd_config',
        require => Package['openssh-server'],
    }
}
```

The package line creates a puppet resource which ensures that the server
always has the latest version of openssh-server installed.

The service resource handles the ssh service. It monitors the file resource
'sshdconfig' for any changes. If a change does occur to that file, the ssh
service is reloaded. Before puppet is able to manage the 'ssh' service,
the package openssh-server is required.

The file resource called 'sshdconfig' handles the servers 
/etc/ssh/sshd_config which is a static file served by the puppet master.
The 'sshdconfig' file resource has several attributes such as owner, group and
mode which are the permissions and ownership of the file once it is applied
to the server. Like the ssh service resource, the 'sshdconfig' file
resource depends on the package resource 'openssh-server'.

Using the attribute 'require' for the file and service resources creates
the dependencies between all 3 resources ensuring that the openssh-server
package is installed before the /etc/ssh/sshd_config file is setup and
the service is managed by puppet.

## The Static Config File

Take a decent /etc/ssh/sshd_config, preferbly one that has been modified
for security reasons. Drop this sshd_config file onto the puppetmaster
server in /etc/puppet/modules/sshd/files/sshd_config.

puppetmasterd typically runs a non-root user such as 'puppet'. In order
for puppetmasterd to serve the static sshd_config fil from
/etc/puppet/modules/sshd/files/sshd_config, the correct permissions on
the file and any parent directories has to be set correctly.

```
$ sudo chown puppet: /etc/puppet/modules/sshd/files/sshd_config
$ sudo chmod 600 /etc/puppet/modules/sshd/files/sshd_config
```

## The Node

Within your puppet node manifest, include the new sshd module like so

```
include sshd
```

The class, static file and node manifest should provide you with
enough of an example to continue building classes within puppet that
use static configuration files.
