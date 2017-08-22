Title: Configure openssh to tunnel through another server using SSH
Date: 2011-07-29
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/configure-openssh-to-tunnel-through-another-server-using-ssh/
disqus_identifier: 147 http://www.linuxsysadmintutorials.com/?p=147
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Configure openssh to tunnel through another server using SSH

Ever needed to SSH to a server that you can't connect to directly and would
have to bounce off another host?

<p><img style="float: top; border: 0px solid black; margin: 20px 20px 20px 10px;" src="/images/ssh-tunnel-web-server1.png" alt="SSH Tunnel Proxy"></p> 

Within your local ssh configuration file which is stored in
$HOME/.ssh/config create a SSH host entry for the server you are going
to tunnel through.

```
Host                    ssh-proxy-server
HostName                ssh-proxy-server.example.com
```

The host ssh-proxy-server will need the netcat package installed which
provides the nc binary.

Now create another SSH host entry for the final destination server. In order
to SSH to this server, you have to SSH to the ssh-proxy-server, create a
tunnel then SSH through that tunnel.

```
Host                    web-server
HostName                web-server.example.com
ProxyCommand            ssh -q -A ssh-proxy-server 'nc %h %p'
```

The important line in the final configuration item is the ProxyCommand. 

ProxyCommand specifies the command to use to connect to the server. %h is
substituted for the hostname which is defined by the HostName directive. %p
is substituted for the SSH destination port which defaults to 22.

Now SSH indirectly to web-server.example.com. Use -v to see SSH bounce off
the ssh-proxy-server server.

```
$ ssh -v web-server.example.com
```

If the bounce host, in this example being 'ssh-proxy-server.example.com', was
a SOCKS5 host and not a server running SSH we would use a different
ProxyCommand directive.

```
Host                    web-server
HostName                web-server.example.com
ProxyCommand            connect -R both -S ssh-proxy-server.example.com:1080 %h %p 
```

The ProxyCommand above uses the 'connect' binary which creates a connection
to a SOCKS4/5 proxy. Our SSH client will then tunnel over this SOCKS tunnel
to the remote server.
