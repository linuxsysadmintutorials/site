Title: Edit the default User ID (UID) values for new users
Date: 2011-11-11
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/edit-the-default-user-id-uid-values-for-new-users/
disqus_identifier: 577 http://www.linuxsysadmintutorials.com/?p=577
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Edit the default User ID (UID) values for new users

Linux restricts UID values that can be assigned to system users based on
2 values that are set in /etc/login.defs.

```
#
# Min/max values for automatic uid selection in useradd
#
UID_MIN                   500
UID_MAX                 60000
```

Edit UID_MIN to change the value of the lowest ID that useradd will assign
to new system users. Changing the value of UID_MAX will increase the value
that a system administrator can assign to a user.
