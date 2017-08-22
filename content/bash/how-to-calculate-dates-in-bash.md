Title: How to calculate dates in bash
Date: 2011-08-11
disqus_url: http://www.linuxsysadmintutorials.com/how-to-calculate-dates-in-bash/

Add 5 minutes to the current date.

```
$ date --date 'now +5 mins'
```

Add 4 days, 3 hours, 44 minutes and 3 seconds from 2011-01-01 07:00:00.

```
$ date --date "2011-01-01 07:00:00 3 days 3 hours 44 mins 3 secs"
```

Subtract 5 minutes from the current date.

```
$ date --date 'now -5 mins'
```

Subtract 5 days, 8 hours, 31 minutes and 45 seconds from
2011-01-01 08:00:00 and return it in unixseconds.

```
$ date --date '2011-01-01 08:00:00 3 days ago 5 hours ago 31 mins ago 45 secs ago' +%s
```
