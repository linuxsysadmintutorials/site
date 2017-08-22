Title: How to sniff MySQL traffic on a database server
Date: 2012-10-07
Status: Published
disqus_url: http://www2.linuxsysadmintutorials.com/how-to-sniff-mysql-traffic-on-a-database-server
disqus_identifier: 493 http://www.linuxsysadmintutorials.com/?p=493
disqus_container_id: disqus_thread
disqus_domain: disqus.com
disqus_shortname: wwwlinuxsysadmintutorials
disqus_title: How to sniff MySQL traffic on a database server

tshark, a terminal based version of wireshark, will dump MySQL statements which it
captures on port 3306 to the terminal.

As root, run the following command on your database server to capture all SQL
statements that are being passed to it from MySQL clients on internet eth0.

```
# tshark -i eth0 \
    -aduration:60 \
    -d tcp.port==3306,mysql \
    -T fields \
    -e mysql.query 'port 3306'
```
