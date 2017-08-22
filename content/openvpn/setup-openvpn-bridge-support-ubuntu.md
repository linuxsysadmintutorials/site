Title: Setup OpenVPN with Bridging Support on Ubuntu
Date: 2012-06-09
disqus_url: http://www.linuxsysadmintutorials.com/setup-openvpn-with-bridging-support-on-ubuntu/

OpenVPN is the de facto standard when it comes to deploying secure VPNs with
Linux. OpenVPN has Windows and Mac clients which makes it a perfect VPN
solution to serve most workstations and servers.

This tutorial will setup OpenVPN on a Ubuntu server allowing a remote
workstation to connect to the VPN. The workstation will be bridged into the
local network over the OpenVPN tunnel giving it a local IP address
on the network.

<p><img style="float: center; border: 0; margin: 20px 20px 20px 10px;" src="/images/OpenVPN-road-warrior-bridge-diagram.png" alt="OpenVPN road warrior bridge diagram"></p>

# Server Setup

First, install the OpenVPN and bridge tools packages on the server.


```
# apt-get install openvpn bridge-utils
```

In order to assign the remote workstation a 192.0.2.0/24 address we need to
be able to bridge the OpenVPN interface (tap0) to the local
192.0.2.0/24 network. The adding of the tap0 interface to the bridge 
interface br0 will be handled by OpenVPN. 

Within the servers network configuration file /etc/network/interfaces we
need to create the br0 interface.

```
auto br0
iface br0 inet static
    address 192.0.2.1
    netmask 255.255.255.0
    network 192.0.2.0
    broadcast 192.0.2.255
    bridge_ports eth0 
    bridge_fd 9
    bridge_hello 2
    bridge_maxage 12
    bridge_stp on
    bridge_prio 1000
```

Bring up the br0 interface with ifup. If eth0 was already configured on
the local network, this NIC will have to be brought down before bringing
up br0. 

```
# ifup br0
```

Check out the <a href="https://help.ubuntu.com/community/NetworkConnectionBridge">Network Connection Bridge</a> Ubuntu wiki page if you are after more information on bridging.

Import the easy-rsa scripts which are shipped with the OpenVPN package and
stored in /usr/share/doc. Also setup a vars file which will be sourced
when creating certificates.

```
# mkdir /etc/openvpn/easy-rsa
# rsync -avP /usr/share/doc/openvpn/examples/easy-rsa/2.0/* /etc/openvpn/easy-rsa/ 
```

Add a set of default variables to /etc/openvpn/easy-rsa/vars which will
be sourced when creating certificates.

**/etc/openvpn/easy-rsa/vars**

```
export KEY_COUNTRY="AU"
export KEY_PROVINCE=VIC
export KEY_CITY=MELBOURNE
export KEY_ORG="OpenVPN-EXAMPLECOMPANY"
export KEY_EMAIL="admin@example.org"
```

Setup the CA, its certificates and the shared secret key storing them in
/etc/openvpn/

```
# chown -R root:admin /etc/openvpn/easy-rsa
# chmod g+w /etc/openvpn/easy-rsa
# cd /etc/openvpn/easy-rsa
# source ./vars
# sh ./clean-all
# sh ./build-dh
# sh ./pkitool --initca
# sh ./pkitool --server server
# cd keys
# openvpn --genkey --secret ta.key
# cp server.crt server.key ca.crt dh1024.pem ta.key /etc/openvpn/ 

```

Create the scripts that will execute when the OpenVPN service starts and stops. These scripts add and remove the OpenVPN interface to the servers br0 interface.

**/etc/openvpn/down.sh**

```bash
#!/bin/sh
PATH=/sbin:/usr/sbin:/bin:/usr/bin
BR=$1
DEV=$2
brctl delif $BR $DEV
ip link set "$DEV" down
```

**/etc/openvpn/up.sh**

```bash
#!/bin/sh
PATH=/sbin:/usr/sbin:/bin:/usr/bin
BR=$1
DEV=$2
MTU=$3
ip link set "$DEV" up promisc on mtu "$MTU"
if ! brctl show $BR | egrep -q "\W+$DEV$"; then
    brctl addif $BR $DEV
fi
```

It's important that both of these scripts are executable.

```
# chmod a+x /etc/openvpn/down.sh /etc/openvpn/up.sh
```

The server configuration for OpenVPN lives in /etc/openvpn/server.conf. The
config below bridges the OpenVPN clients to the 192.0.2.1/24 network using a
range between 192.0.2.160 and 192.0.2.170 to lease to OpenVPN clients. It 
also calls the up.sh and down.sh scripts when the OpenVPN service is started 
and stopped.

**/etc/openvpn/server.conf**

```
port 1194
proto udp
server-bridge 192.0.2.1 255.255.255.0 192.0.2.160 192.0.2.170
dev tap0
ca ca.crt
cert server.crt
tun-mtu 1454
key server.key  
dh dh1024.pem
up "/etc/openvpn/up.sh br0"
down "/etc/openvpn/down.sh br0"
ifconfig-pool-persist ipp.txt
keepalive 10 600
comp-lzo
persist-key
persist-tun
verb 3
mute 20
status openvpn-status.log
client-config-dir ccd
client-to-client
```

Once the config and the server certificates are in place, start the OpenVPN
service.

```
# service openvpn start
```

# Client Setup

On the server generate the client certificates for a client named John Example.

```
# cd /etc/openvpn/easy-rsa
# source ./vars
# ./build-key-pass john_example
```

Enter the PEM pass phrase that the client will need to enter in everytime he
connects to the VPN. The other options can be left as the default except for
the Common Name which should be set to **john_example.ovpn**

Tar the certificates up and transfer them to the client work station.

```
# cd /etc/openvpn/easy-rsa/keys
# tar cvf ~/john_example.tar john_example.{crt,csr,key} ca.crt 
```

With a Linux workstation, drop the files into /etc/openvpn/ after installing
the openvpn package.

On the client generate the OpenVPN client configuration file. 

**/etc/openvpn/client.conf**
```
client
dev tap
proto udp
remote vpn-headoffice.example.org 1194
tun-mtu 1454
nobind
persist-tun
ca ca.crt
cert client-john_example.crt
key client-john_example.key
comp-lzo
verb 3
mute 20
auth-nocache
```

The remote end point for the tunnel is vpn-headoffice.example.org which is
the 203.0.113.254 interface on server.hq.example.org

Once the client configuration is in place, start up the tunnel by starting
OpenVPN service on the Linux workstation.

```
# service openvpn start
```

On a Linux client a tap0 interface is created and should be assigned a
192.0.2.160/24 address allowing the entire 192.0.2.0/24 to be routed
across the OpenVPN connection.

```
$ ip addr show tap0
10: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1454 qdisc pfifo_fast state UNKNOWN qlen 100
    link/ether f2:24:a0:41:51:f0 brd ff:ff:ff:ff:ff:ff
    inet 192.0.2.160/24 brd 192.0.2.255 scope global tap0
    inet6 fe80::fc24:adff:fe43:51f0/64 scope link 
```
