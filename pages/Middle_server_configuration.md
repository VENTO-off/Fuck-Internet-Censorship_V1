# Middle server configuration guide

> **Note!** Linux based operating system must be installed on the server.

## 1. Configuring firewall (iptables)

### 1.1 Forward proxy connections
> **Note!** Set correct port and IP address of the server with proxy installed.
```shell
iptables -t nat -A PREROUTING -p tcp --dport 1337 -j DNAT --to-destination 127.0.0.1:1337 
```
```shell
iptables -t nat -A POSTROUTING -j MASQUERADE 
```

### 1.2 Forward VPN connections
> **Note!** Set correct IP address of the server with VPN installed.
```shell
iptables -t nat -A PREROUTING -p tcp --dport 1723 -j DNAT --to-destination 127.0.0.1:1723
```
```shell
iptables -t nat -A PREROUTING -p gre -j DNAT --to-destination 127.0.0.1
```
```shell
iptables -t nat -A POSTROUTING -j MASQUERADE
```
```shell
iptables -A INPUT -p gre -j ACCEPT
```
```shell
iptables -A FORWARD -p gre -j ACCEPT
```

### 1.3 Save firewall rules
```shell
iptables-save
```

### 1.4 Save firewall rules permanently
```shell
apt-get install iptables-persistent
```

## 2. Configuring network

### 2.1 Enable IP forwarding and connection track helper
Open configuration file `/etc/sysctl.conf` and add the next lines:
```
net.ipv4.ip_forward=1
net.netfilter.nf_conntrack_helper=1
```

### 2.2 Apply the changes
```shell
sysctl -p
```

## 3. Configuring modules

### 3.1 Start PPTP module
```shell
modprobe ip_nat_pptp
```

### 3.2 Enable PPTP module on boot
Open configuration file `/etc/modules` and add the next line:
```
ip_nat_pptp
```
