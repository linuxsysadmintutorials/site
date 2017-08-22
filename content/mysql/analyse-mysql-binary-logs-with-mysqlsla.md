Title: Analyse MySQL binary logs with mysqlsla
Date: 2011-08-28
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/analyse-mysql-binary-logs-with-mysqlsla/
disqus_identifier: 107 http://www.linuxsysadmintutorials.com/?p=107
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Analyse MySQL binary logs with mysqlsla

Pipe MySQL binary logs using  start and stop time into the MySQL log analysis
tool, <a href="https://github.com/daniel-nichter/hackmysql.com/tree/master/mysqlsla">mysqlsla</a>.

```
$ mysqlbinlog --start-datetime='2011-01-01 00:00:00' \
    --stop-datetime='2011-01-01 01:00:00' \
    server-bin-log.000001 | \
    mysqlsla -lt binary - --top 30
```
