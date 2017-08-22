Title: Manage SSHD with puppet using a template confiration file.
Date: 2011-09-01
Status: draft

In a [previous](http://www2.linuxsysadmintutorials.com/create-a-simple-puppet-class-to-maintain-sshd/) tutorial, a puppet sshd class was created which had 3 resources.

 - a package resource to manage the openssh-server package.
 - a service resource to manage the ssh service.
 - a file resource to manage the /etc/ssh/sshd_config file.

```
class sshd { 
    package { 'openssh-server': 
        ensure => latest 
    } 
    service { 'ssh': 
        subscribe => File[sshdconfig], 
        require   => Package['openssh-server'], 
    } 
    file { 'sshdconfig': 
        name    => '/etc/ssh/sshd_config', 
        owner   => root, 
        group   => root, 
        mode    => 644, 
        source  => 'puppet:///sshd/sshd_config', 
        require => Package['openssh-server'], 
    } 
}
```

Using the ‘source’ attribute within our ‘sshdconfig’ file resource, puppet uses a static file which is dropped onto all our nodes in puppet. Using a static file for service configuration doesnt give us much room to have separate SSH configuration directives for our servers. All servers that subscribe to our puppet manifest will inherit this static /etc/ssh/sshd_config configuration file.

Templates help solve this problem by filling in variables from our client systems or our manifest. Puppet handles templates by using ERBs which is the ruby templating method.

Taking our slab of manifest for our sshd class above, we can extend onto it by building a function that handles our sshd_config using templates.

```
define sshd::server::config ( 
    port = "22", 
    keyregenerationinterval = "3600", 
    syslogfacility = "AUTHPRIV", 
    loglevel = "info", 
    logingracetime = "120", 
    permitrootlogin = "no", 
    strictmodes = "yes", 
    rsaauthentication = "yes", 
    pubkeyauthentication = "yes", 
    passwordauthentication = "yes", 
    x11forwarding = "no", 
    printmotd = "no", 
    logingracetime = "120", 
    maxstartups = "10", 
    maxauthtries = "5" ) { 
    file { "/etc/ssh/sshd_config": 
        owner   => root, 
        group   => root, 
        mode    => 600, 
        content => template("sshd/sshd_config.erb"), 
        notify  => Service['sshd'] 
    } 
}
```

The above manifest is called a define and can be safely added to the sshd class after the last closing bracket (‘}’). View it as a function which has several optional arguments such as port, logingracetime and maxauthtries. These arguments are used as variables which are passed to the template found in the content attribute of the ‘/etc/ssh/sshd_config’ file resource.

Now build a template file which needs to be placed into /etc/puppet/modules/sshd/templates/sshd_config.erb on the puppetmaster server.

The template file will be based on a standard openssh server configuration file though it will be parsed by the puppet templating system which has access to variables which we control.

```
Port <%= port %>
Protocol 2 
HostKey /etc/ssh/ssh_host_rsa_key 
HostKey /etc/ssh/ssh_host_dsa_key 
UsePrivilegeSeparation yes 
KeyRegenerationInterval <%= keyregenerationinterval %>
ServerKeyBits 768 
SyslogFacility <%= syslogfacility %>
LogLevel <%= loglevel %>
LoginGraceTime <%= logingracetime %>
PermitRootLogin <%= permitrootlogin %> 
StrictModes <%= strictmodes %>
...
```


The above is from the sshd_config.erb (located in /etc/puppet/modules/sshd/templates/sshd_config.erb on our puppet master server) template file and closely resembles a standard openssh server configuration file. In place of some of the options to directives such as Port and PermitRootLogin we’ve used <%= puppet_variable_name %> (example; ‘<%= permitrootlogin %>’). When the puppet manifest is compiled by the puppet nodes, these puppet variable names are substituted to what we pass to the defined sshd::server::config function.

Before we start using the new defined sshd::server::config function which manages ‘/etc/ssh/sshd_config’ for us dynamically, we should remove the ‘/etc/ssh/sshd_config’ file resource in our sshd class.

Once the static ‘/etc/ssh/sshd_config’ file resource is removed we can begin to use our templated version.

```
sshd::server::config { "/etc/ssh/sshd_config": 
    logingracetime  => '60', 
    permitrootlogin => 'no', 
    x11forwarding   => 'yes', 
}
```

This slab of manifest calls the defined sshd::server::config function creating a resource called Sshd::server::config[‘/etc/ssh/sshd_config’]. It passes 3 options to our sshd::server::config function.

 - logingracetime which used for the LoginGraceTime sshd_config directive setting the nuumber of seconds the sshd server will disconnect the user if she has not successfully logged in.
 - permitrootlogin which translates to the PermitRootLogin sshd_config directive specifying whether root can log in using ssh.
 - x11forwarding which manages the sshd_config directive X11Forwarding specifying whether X11 forwarding is permitted.


