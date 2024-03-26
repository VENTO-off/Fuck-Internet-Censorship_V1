# PPTP VPN installation guide
> **Warning!** PPTP VPN protocol is not secured! It used just to bypass Internet restrictions.

> **Note!** Linux based operating system must be installed on the server.

## 1. VPN installation
```shell
apt-get install pptpd
```

## 2. Adding VPN users
Open configuration file `/etc/ppp/chap-secrets` and add new users:
```
username * password *
```

## 3. Configuring DNS
Open configuration file `/etc/ppp/pptpd-options` and add the next DNS records:
```
ms-dns 8.8.8.8
ms-dns 8.8.4.4
```

## 4. Configuring connection timeout
Open configuration file `/etc/ppp/pptpd-options` and modify the necessary parameter (timeout in seconds):
```
idle 900
```

## 5. Configuring firewall (iptables)

### 5.1 Allow VPN connections
```shell
iptables -A INPUT -i eth0 -p tcp --dport 1723 -j ACCEPT
```
```shell
iptables -A INPUT -i eth0 -p gre -j ACCEPT
```
```shell
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
```shell
iptables -A FORWARD -i ppp+ -o eth0 -j ACCEPT
```
```shell
iptables -A FORWARD -i eth0 -o ppp+ -j ACCEPT
```

### 5.2 Save firewall rules
```shell
iptables-save
```

### 5.3 Save firewall rules permanently
```shell
netfilter-persistent save
```

## 6. Configuring network

### 6.1 Enable IP forwarding
Open configuration file `/etc/sysctl.conf` and uncomment the next line:
```
net.ipv4.ip_forward=1
```

### 6.2 Apply the changes
```shell
sysctl -p
```

## 7. Starting VPN
```shell
/etc/init.d/pptpd start
```
