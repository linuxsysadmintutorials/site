=================================================================================
How to retrieve the SHA1 fingerprint of a remote server certificate using OpenSSL
=================================================================================

:date: 2016-11-20
:tags: openssl
:category: openssl

I needed to retrieve the SHA1 fingerprint of a remote IMAPS server I was
working with. This command did the trick::

    $ echo | openssl s_client -showcerts -connect example.org:993 </dev/null \
        | openssl x509 -fingerprint

By changing the server address and port, openssl is also able to query other services
which use TLS::

    $ echo | openssl s_client -showcerts -connect google.com:443 </dev/null \
        | openssl x509 -fingerprint

To get the SHA-256 fingerprint, I had to first download the certificate and
then use `-fingerprint -sha256`::

    $ echo | openssl s_client -showcerts -connect google.com:443 </dev/null \
        | openssl x509 > google.com.crt
    $ openssl x509 -noout -fingerprint -sha256 -inform pem -in google.com.crt 
    SHA256 Fingerprint=F2:81:54:13:73:3A:7B:18:33:B1:49:C5:D8:A8:14:68:5B:A1:E1:24:C6:CC:0D:45:EA:0E:A6:A7:AA:7A:C1:08

And then MD5::

    $ openssl x509 -noout -fingerprint -md5 -inform pem -in google.com.crt 
    MD5 Fingerprint=05:97:71:33:F6:18:3F:C1:50:8F:1E:79:47:F9:B6:57
