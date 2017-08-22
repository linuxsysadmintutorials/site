Configure the I2p i2ptunnel HTTP proxy to listen on all interfaces
##################################################################

:date: 2016-11-12
:tags: i2p
:category: i2p

By default, the i2p HTTP Proxy client tunnel will listen on 127.0.0.1:4444::

    # lsof -n -i:4444
    COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
    java    2078 i2psvc  117u  IPv6  75875      0t0  TCP 127.0.0.1:4444 (LISTEN)

To configure the i2p HTTP Proxy client tunnel to listen on all interfaces
navigate to the i2ptunnel configuration section by visiting the I2P Router
Console (listening on port 4444) > Web Apps > i2ptunnel > I2P HTTP Proxy. The
change the Reachable by address within the dropdown menu from 127.0.0.1 to
0.0.0.0.

Click Save then Restart All.
