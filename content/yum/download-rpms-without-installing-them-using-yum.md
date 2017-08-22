Title: How to download RPMs without installing them using yum.
Date: 2013-08-03
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/how-to-download-rpms-without-installing-them-using-yum/
disqus_identifier: 807 http://www.linuxsysadmintutorials.com/?p=807
disqus_container_id: disqus_thread
disqus_domain: disqus.com
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: How to download RPMs without installing them using yum

You may want to download RPMs only and not install them using yum. Perhaps 
you're within a change freeze but would like to 'warm' up your yum cache
to make your production security upgrades alot faster and smoother.

The yum-plugin-downloadonly yum plugin provides yum with a downloadonly option.

```
# yum install yum-plugin-downloadonly
# yum install --downloadonly puppet
```

You can also download the RPMs to a directory of your choosing and not
/var/cache/yum.

```
# yum install --downloadonly --downloaddir=/var/tmp/puppet puppet
```
