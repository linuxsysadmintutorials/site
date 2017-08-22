Title: Enable EPEL on a CentOS/RHEL 6 server
Date: 2013-07-02
disqus_url: http://www2.linuxsysadmintutorials.com/enable-epel-on-a-centosrhel-6-server/
disqus_identifier: 803 http://www.linuxsysadmintutorials.com/?p=803
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Enable EPEL on a CentOS\/RHEL 6 server

[Extra Packages for Enterprise Linux (EPEL)](https://fedoraproject.org/wiki/EPEL)
is the group that creates, maintains and manages high quality packages for
RHEL, CentOS and Scientific Linux.

To enable the repo all that is needed is the [epel-release](https://dl.fedoraproject.org/pub/epel/6/x86_64/repoview/epel-release.html) RPM.

```
# rpm -ivh https://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
```

The epel-release RPM will setup the RPM EPEL gpg key and the required yum
repo files.
