Title: Ignore host keys when connecting to an OpenSSH server
Date: 2014-09-18
Status: Published
disqus_url: http://www.linuxsysadmintutorials.com/ignore-host-keys-when-connecting-to-an-openssh-server
disqus_identifier: 927 http://www.linuxsysadmintutorials.com/?p=927
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Ignore host keys when connecting to an OpenSSH server


Have you ever used SSH within a script or perhaps with rsnapshot and had to accept the servers host key?

```
$ ssh server.example.org
The authenticity of host 'server.example.org (192.0.2.100)' can't be established.
ECDSA key fingerprint is 63:f1:fe:d1:a5:4e:2e:94:33:13:46:58:9f:51:32:f1.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'server.example.org,192.0.2.0' (ECDSA) to the list of known hosts.
```

By using <strong>StrictHostKeyChecking</strong> and <strong>UserKnownHostsFile</strong> SSH client options you can bypass this check.

```
$ ssh server.example.org -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null
Warning: Permanently added 'server.example.org (192.0.2.100)' (ECDSA) to the list of known hosts.
root@server:~#
```

Alternatively you could update ${HOME}/.ssh/config

```
Host                    server.example.org
    HostName                server.example.org
    User                    root
    Compression             yes
    ForwardAgent            no
    ForwardX11              no
    StrictHostKeyChecking   no
    UserKnownHostsFile      /dev/null
```

<h2>Security Warning</h2>

It's important that you trust the server that you are connecting to before changing the default StrictHostKeyChecking & UserKnownHostsFile options. All an attacker would need to do is poison your DNS and import your SSH public key to fake what you would expect to be the server you are connecting to.
