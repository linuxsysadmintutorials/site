Title: Update the timezone in CentOS 6
Date: 2014-09-22
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/update-the-timezone-in-centos-6
disqus_identifier: 958 http://www.linuxsysadmintutorials.com/?p=958
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Update the timezone in CentOS 6

The timezone a server is configured with can be shown with the date command.

```
$ date +%Z
EDT
```

To change the timezone in CentOS 6, first ensure you have the latest tzdata
package installed.

```
$ yum install tzdata
```

Then update the /etc/localtime with the new timezone by copying 
the timezone file the tzdata package.

```
$ cp /usr/share/zoneinfo/UTC /etc/localtime
$ date +%Z
UTC
```
