Title: Display overhead of all tables in a MySQL database
Date: 2011-09-01
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/display-overhead-of-all-tables-in-a-mysql-database/
disqus_identifier: 18 http://www.linuxsysadmintutorials.com/blog/?p=18
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Display overhead of all tables in a MySQL database


MySQL overhead is white space within a database table that is left over from when data is deleted. Displaying the overhead in megabytes for all tables in a database is done with this command.

```
$ mysql -u $USERNAME --password='$PASSWORD' -e 'show table status from $DATABASE' \
    | awk '{printf("%f %s\n", ($10 / (1024*1024)),$1)}' \
    | sort -g
```
