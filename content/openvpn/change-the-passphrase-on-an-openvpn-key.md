Title: Change the passphrase on an OpenVPN key
Date: 2012-06-23
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/change-the-passphrase-on-an-openvpn-key/
disqus_identifier: 483 http://www.linuxsysadmintutorials.com/?p=483
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Change the passphrase on an OpenVPN key

OpenVPN keys are generated using openssl and can be edited using the openssl command.

To change the passphrase on the key use openssl to write out a new key and move it into place.

```
$ openssl rsa -des3 -in /etc/openvpn/client.key -out /etc/openvpn/client-new.key
...
$ mv /etc/openvpn/client-new.key /etc/openvpn/client.key
```

To remove the passphrase all together, leave -des3 out of the openssl command.

```
$ openssl rsa -in /etc/openvpn/client.key -out /etc/openvpn/client-new.key
```
