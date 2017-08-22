Title: How to check the expiry of a HTTP SSL certificate on the command line with openssl?
Date: 2012-02-09
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/how-to-check-the-expiry-of-a-http-ssl-certificate/
disqus_identifier: 387 http://www.linuxsysadmintutorials.com/?p=387
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: How to check the expiry of a HTTP SSL certificate

Retrieve the SSL certificate with openssl.

```
$ echo GET | openssl s_client -connect www.google.com:443 > cert.txt
```

Next extract the end date which is when the certificate will expire.

```
$ openssl x509 -in cert.txt -noout -enddate
notAfter=Sep 30 23:59:59 2013 GMT

```
