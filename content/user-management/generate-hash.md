Title: How to generate a password used in /etc/shadow
Date: 2015-12-03
Status: draft


Install the apg and whois packages.

```
# apt-get install apg whois
```

```
# mkpasswd  -m sha-512 -S $(apg -n1) -s <<< PASSWORD
```
