Title: Setup PAM authentication with OpenVPN's auth-pam module
Date: 2014-09-23
disqus_url: http://www.linuxsysadmintutorials.com/setup-pam-authentication-with-openvpns-auth-pam-module/
disqus_identifier: 960 http://www.linuxsysadmintutorials.com/?p=960
disqus_shortname: wwwlinuxsysadmintutorials

The OpenVPN <a href="https://community.openvpn.net/openvpn/browser/plugin/auth-pam?rev=6cfada268122fe54ce6d211d96c744e91d41248c&order=name">auth-pam</a>
module provides an OpenVPN server the ability to hook into Linux
<a href="http://www.linux-pam.org/">PAM</a> modules adding a powerful
authentication layer to OpenVPN.

Adding support to your existing OpenVPN server config is simple.

On the server, add the following to the OpenVPN config.

```
plugin /usr/lib64/openvpn/plugins/openvpn-plugin-auth-pam.so openvpn
```

For Ubuntu and Debian distributions, the path to the plugin is /usr/lib/openvpn/openvpn-plugin-auth-pam.so.

Create a new PAM service file located at /etc/pam.d/openvpn.

```
auth    required        pam_unix.so    shadow    nodelay
account required        pam_unix.so
```

On the client, add the following to the OpenVPN config.

```
auth-user-pass
```

Restart the OpenVPN server and that should be it. Any new OpenVPN connections
will first be authenticated with pam_unix.so so the user will need a
system account.

If the OpenVPN server exits with the log below after an authentication
attempt, you most likely are running OpenVPN within a chroot and have not
created a tmp directory.

```
Wed Sep 17 19:58:15 2014 us=704365 10.40.10.3:33518 Could not create temporary file '/tmp/openvpn_acf_5a98375273019ab2d5d300dabcf91c91.tmp': No such file or directory
```

Simply create a tmp directory within the chroot with the permissions that
match your OpenVPN server config.

```
$ grep -E "(^chroot|^user|^group)" /etc/openvpn/server.conf
chroot /var/lib/openvpn
user openvpn
group openvpn
$ mkdir --mode=0700 -p /var/lib/openvpn/tmp
$ chown openvpn:openvpn /var/lib/openvpn/tmp
```

# Extending the OpenVPN PAM service

You can extend the use of PAM by adding to the /etc/pam.d/openvpn file.

```
auth    required        pam_unix.so    shadow    nodelay
auth    requisite       pam_succeed_if.so uid >= 500 quiet
auth    requisite       pam_succeed_if.so user ingroup wheel quiet
auth    required        pam_tally2.so deny=4 even_deny_root unlock_time=1200
account required        pam_unix.so
```

 - line 2 requires that the user's uid is greater than or equal to 500.
 - line 3 requires that the user belongs to the wheel group.
 - line 4 uses pam_tally2 to lock out the account after 4 consecutive
   unsuccessful authentication attempts. After 1200 seconds, the counter
   will reset back to 0.

