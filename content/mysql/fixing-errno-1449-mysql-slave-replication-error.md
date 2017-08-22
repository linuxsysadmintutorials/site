Title: Fixing errno 1449 MySQL slave replication error
Date: 2014-09-14
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/fixing-errno-1449-mysql-slave-replication-error/
disqus_identifier: 911 http://www.linuxsysadmintutorials.com/?p=911
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: Fixing errno 1449 MySQL slave replication error

I was notified that one of my slave MySQL DB's stop replicating. An
immediate investigation showed that because I dont slave the mysql.user
table (is there a reason to do this?), an SQL statement that required a
certain user and privilege failed on the slave and broke replication.

```
mysql-slave> SHOW SLAVE STATUS\G;
...
Last_Errno: 1449
Last_Error: Error 'There is no 'user_foo'@'ip_bar' registered' on query.
Default database: 'database_fye'. Query: 'insert into ...'
...
```

Ouch.

To fix this I stopped the replication and took note of the master log
position, <strong>Exec_Master_Log_Pos</strong> and slave's master log
file, <strong>Relay_Log_File</strong>. Both are found in the output of
<strong>SHOW SLAVE STATUS</strong>.

I then added the mysql user account with appropriate privileges, reset
the slave, manually pointed mysql to the master log position and then
started to slave again.

```
mysql-slave> STOP SLAVE;
mysql-slave> SHOW SLAVE STATUS\G;
mysql-slave> GRANT ALL PRIVILEGES ON database_fye.* TO user_foo@'ip_bar'
IDENTIFIED BY 'password_foe';
mysql-slave> FLUSH PRIVILEGES;
mysql-slave> RESET SLAVE;
mysql-slave> CHANGE MASTER TO MASTER_HOST='MASTER_IP',
MASTER_USER='SLAVE_USER', MASTER_PASSWORD='SLAVE_PASS',
MASTER_LOG_FILE='mysql-bin.000801', MASTER_LOG_POS=17122711;
mysql-slave> START SLAVE;
```

A couple of seconds later the slave caught up with the master.

