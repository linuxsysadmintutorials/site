Title: Install and configure php5 fpm to run under a system user account using nginx as the web server on Ubuntu Trusty 14.04.
Date: 2015-11-12
Status: published

This tutorial will setup php5-fpm running under a system account and
configure nginx to use the pool.

Whats php-fpm? From [php-fpm.org](http://php-fpm.org/):

    PHP-FPM (FastCGI Process Manager) is an alternative PHP FastCGI
    implementation with some additional features useful for sites of any
    size, especially busier sites.

First setup a user which will run php5-fpm.

```
:::bash
# adduser --system --shell=/bin/false \
    --home=/var/run/exampleuser \
    --ingroup nogroup \
    exampleuser
```

Next, install the php5-fpm package.

```
# apt-get install --yes php5-fpm
```

Copy the default php-fpm www.conf pool file.

```
# cp /etc/php5/fpm/pool.d/www.conf \
    /etc/php5/fpm/pool.d/exampleuser.conf
```

Within the /etc/php5/fpm/pool.d/exampleuser.conf file, change the variable
pool name to match the username.


```
; Start a new pool named 'www'.
; the variable $pool can we used in any directive and will be replaced by the
; pool name ('www' here)
[exampleuser]

```

Change the user and group variables to match the new username and group
it belongs to.


```
; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;       will be used.
user = exampleuser
group = nogroup
```
Change the socket path to be within the new users home directory.

```
listen = /var/run/exampleuser/php5-fpm.sock
```

Restart the php5-fpm service.

```
# service php5-fpm restart
```

nginx will need to make use of the php5-fpm socket file. Within the server nginx block add a location directive which points to the socket.


```
:::bash
location ~\.php$ {
    fastcgi_split_path_info ^(.+\.php)(.*)$;
    fastcgi_pass    unix:/var/run/exampleuser/php5-fpm.sock;
    fastcgi_index   index.php;
    include         /etc/nginx/fastcgi_params;
}
```

Restart the nginx service.

```
# service nginx restart
```
