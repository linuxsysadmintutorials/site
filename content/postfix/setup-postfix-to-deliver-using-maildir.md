Title: Setup postfix to deliver using Maildir
Date: 2012-10-29
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/setup-postfix-to-deliver-using-maildir/
disqus_identifier: 501 http://www.linuxsysadmintutorials.com/?p=501
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Setup postfix to deliver using Maildir

Maildir is the more efficient way to store email on a server, storing each individual email as its own file, as opposed to mailbox which concatenates all email into a single file

To configure postfix to deliver email to a users Maildir, add the following configure lines to /etc/postfix/main.cf

```
home_mailbox = Maildir/
```

Reload postfix.

```
$ sudo service postfix reload
```
