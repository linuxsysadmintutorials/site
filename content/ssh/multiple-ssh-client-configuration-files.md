Title: Multiple SSH client configuration files
Date: 2013-01-13
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/multiple-ssh-client-configuration-files/
disqus_identifier: 629 http://www.linuxsysadmintutorials.com/?p=629
disqus_container_id: disqus_thread
disqus_domain: disqus.com
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Multiple SSH client configuration files

Having many hosts managed through your ${HOME}/.ssh/config file can quickly become a wildly beast of a configuration file to maintain with each host containing a few SSH client options.

```
Host                    web01
    HostName                web01.prod.example.com
    User                    rene
    Compression             yes
    ForwardAgent            no
    IdentityFile            ~/.ssh/production
    ForwardX11              no
```

OpenSSH won’t allow you to split out the configuration in multiple files, such as ~/.ssh/config.dev, ~/.ssh/config.production and ~/.ssh/config.test and then include them within your main SSH configuration file ~/.ssh/config with a directive such as Include like Apache configuration allows you.

A [bug report](https://bugzilla.mindrot.org/show_bug.cgi?id=1585) has been logged with the OpenSSH team though this has got no traction yet.

With a bash alias and a simple ssh client wrapper script, you can create a work around that will allow you to split up SSH client configuration into several files.

First, create the SSH client wrapper. I drop this file into ~/bin/ssh and ensure it is executable.

```null
#!/bin/bash

ssh() {
    TMPDIR=~/tmp
    case "$(uname -s)" in
        Linux)
            tmp_fifo=$(mktemp -u --suffix=._ssh_fifo)
            ;;
        Darwin)
            tmp_fifo=$(mktemp -u -t ._ssh_fifo)
            ;;
        *)
            echo 'unsupported OS'
            exit
            ;;
    esac

    # cleanup first
    rm ~/tmp/._ssh_fifo* 2>/dev/null

    mkfifo "$tmp_fifo" 
    cat ~/.ssh/config ~/.ssh/config.* >"$tmp_fifo" 2>/dev/null & 
    /usr/bin/ssh -F "$tmp_fifo" "$@"
    rm "$tmp_fifo"
}

ssh $*
```

The SSH client wrapper creates a FIFO file consisting of all my ~/.ssh/config* files concatenated. The wrapper then calls the proper SSH client binary passing the FIFO file as the alternative configuration file.

So my shell is forced to call this wrapper, I create a simple bash alias in ~/.bash_profile


```
oalias ssh='~/bin/ssh'
```

Now everytime ssh is called, all configuration files in ~/.ssh/config* are available allowing me to split out my SSH hosts into their own separate file.

This solution came about from the answer provided by [estani](https://superuser.com/users/129103/estani) on the superuser.com question [Is there a way for one SSH config file to include another one?](https://superuser.com/questions/247564/is-there-a-way-for-one-ssh-config-file-to-include-another-one).


