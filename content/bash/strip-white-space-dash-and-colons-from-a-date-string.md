Title: Strip white space, dash and colons from a date string
Date: 2011-08-18
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/strip-white-space-dash-and-colons-from-a-date-string/
disqus_identifier: 121 http://www.linuxsysadmintutorials.com/?p=121
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Strip white space, dash and colons from a date string

Pipe a timestamp string into sed combined to remove the -, : and white space to get a string which is suitable for a log file.

```
$ echo -n "2011-08-10 08:50:00" | sed 's/-\|:\|\s//g'
20110810085000
```
