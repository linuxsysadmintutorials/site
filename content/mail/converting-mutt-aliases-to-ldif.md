Title: Converting mutt aliases to ldif
Date: 2011-08-08
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/converting-mutt-aliases-to-ldif/
disqus_identifier: 40 http://www.linuxsysadmintutorials.com/?p=40
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Converting mutt aliases to ldif

mutt aliases can be converted to ldif format to allow them to be imported to address books that support ldif such as the address book that ships with thunderbird.

First install abook.

```
# apt-get install abook
```

Then convert your mutt aliases file with abook to ldif.

```
$ abook --convert --infile ~/.mutt/aliases \
    --informat mutt \
    --outfile ~/aliases.ldif \
    --outformat ldif
```

This will create ~/aliases.ldif which can be imported into thunderbird.
