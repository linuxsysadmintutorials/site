Configure the I2P web console to listen on all interfaces
#########################################################

:date: 2016-11-12
:tags: i2p
:category: i2p

By default, the i2p web console application will listen on 127.0.0.1:7657::

    # lsof -n -i:7657
    COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
    java    1150 i2psvc   86u  IPv6  17751      0t0  TCP [::1]:7657 (LISTEN)
    java    1150 i2psvc   90u  IPv6  17753      0t0  TCP 127.0.0.1:7657 (LISTEN)

To configure i2p to listen on all interfaces, edit
/var/lib/i2p/i2p-config/clients.config replace::

    clientApp.0.args=7657 ::1,127.0.0.1 ./webapps/

with::

    clientApp.0.args=7657 0.0.0.0 ./webapps/

Then restart the i2p service::

    # systemctl restart i2p.service

lsof will show i2p listening on all interfaces::

    # lsof -n -i:7657
    COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
    java    1604 i2psvc   86u  IPv6  19974      0t0  TCP *:7657 (LISTEN)
