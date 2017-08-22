Title: Convert InnoDB to InnoDB plugin, compressed tables
Date: 2011-09-01
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/convert-innodb-to-innodb-plugin-compressed-tables/
disqus_identifier: 17 http://www.linuxsysadmintutorials.com/blog/?p=17
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Convert InnoDB to InnoDB plugin, compressed tables

Convert standard InnoDB tables to compressed InnoDB tables.

```
$ mysqldump | sed -e 's/^) ENGINE=InnoDB/) ENGINE=InnoDB ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8/'
```
