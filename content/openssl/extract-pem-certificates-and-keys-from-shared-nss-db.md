Title: Extract PEM certificates and keys from a shared NSS DB
Date: 2013-05-21
disqus_url: http://www.linuxsysadmintutorials.com/extract-pem-certificates-and-keys-from-a-shared-nss-db/

Way back in 1994 Netscape developed NSS DB which is a certificate key store
that uses Berkeley DataBase. It's commonly described as "DBM" and 
surprisingly is still in wide use today. 

Certificates and keys are usually stored in /etc/pki/nssdb/cert8.db and
/etc/pki/nssdb/key3.db on Red Hat based systems. 

There is a <a href="https://wiki.mozilla.org/NSS_Shared_DB#Shared_Database_Proposal">proposal</a> to replace this with SQLite databases in which you will
find sqlite database files /etc/pki/nssdb/cert9.db and /etc/pki/nssdb/key4.db.

Extracting the certificates and keys from the DBM files can be done with
<a href="https://developer.mozilla.org/en-US/docs/NSS/tools">NSS tools</a>
and <a href="http://www.openssl.org/">OpenSSL</a>.

Copy the /etc/pki/nssdb/cert8.db and /etc/pki/nssdb/key3.db to a temp
directory and extract the certificate you're after using certutil.

```
# certutil -L -d .
Certificate Nickname                                         Trust Attributes
                                                             SSL,S/MIME,JAR/XPI

FreeIPA CA                                                       CT,C,C
# certutil -L -d . -a -n 'FreeIPA CA' > freeipa.crt
```

The PEM certificate should now be stored in free-ipa.crt.

To extract the PEM key from key3.db use certutil, pk12util and openssl.

```
# certutil -K -d . -a
# pk12util -o keys.p12 -n 'FreeIPA Key' -d .
# openssl pkcs12 -in keys.p12 -out freeipa.key -nodes
```
