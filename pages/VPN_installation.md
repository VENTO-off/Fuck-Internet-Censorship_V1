# VPN installation guide
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
```
iptables -A INPUT -i eth0 -p tcp --dport 1723 -j ACCEPT
```
```
iptables -A INPUT -i eth0 -p gre -j ACCEPT
```
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
```
iptables -A FORWARD -i ppp+ -o eth0 -j ACCEPT
```
```
iptables -A FORWARD -i eth0 -o ppp+ -j ACCEPT
```

### 5.2 Save firewall rules
```
sysctl -p
```
```
iptables-save
```

## 6. Starting VPN
```
/etc/init.d/pptpd start
```