# IKEv2 installation guide

> **Note!** Linux based operating system must be installed on the server.

## 1. VPN installation
```shell
apt-get install strongswan strongswan-pki libcharon-extra-plugins libcharon-extauth-plugins libstrongswan-extra-plugins libtss2-tcti-tabrmd-dev
```

## 2. Creating certificates

### 2.1 Create directories for the certificates
```shell
mkdir -p ~/pki/{cacerts,certs,private}
```

### 2.2 Generate a root key
```shell
pki --gen --type rsa --size 4096 --outform pem > ~/pki/private/ca-key.pem
```

### 2.3 Create and sign a root certificate authority
> **Note!** Set correct IP address of the server with VPN installed.
```shell
pki --self --ca --lifetime 3650 --in ~/pki/private/ca-key.pem --type rsa --dn "CN=127.0.0.1" --outform pem > ~/pki/cacerts/ca-cert.pem
```

### 2.4 Generate a private key
```shell
pki --gen --type rsa --size 4096 --outform pem > ~/pki/private/server-key.pem
```

### 2.5 Create and sign a server certificate
> **Note!** Set correct IP address of the server with VPN installed.
```shell
pki --pub --in ~/pki/private/server-key.pem --type rsa | pki --issue --lifetime 1825 --cacert ~/pki/cacerts/ca-cert.pem --cakey ~/pki/private/ca-key.pem --dn "CN=127.0.0.1" --san "127.0.0.1" --san "127.0.0.1" --flag serverAuth --flag ikeIntermediate --outform pem > ~/pki/certs/server-cert.pem
```

### 2.6 Copy all the certificates to the `/etc/ipsec.d` directory
```shell
cp -r ~/pki/* /etc/ipsec.d/
```

## 3. VPN configuration

### 3.1 Delete default configuration file
```shell
rm -f /etc/ipsec.conf
```

### 3.2 Configure connection settings
Open configuration file `/etc/ipsec.conf` and add the next contents:
```
config setup
	charondebug="ike 1, knl 1, cfg 0"
	uniqueids=never

conn ikev2-vpn
	auto=add
	compress=no
	type=tunnel
	keyexchange=ikev2
	fragmentation=yes
	forceencaps=yes
	dpdaction=clear
	dpddelay=300s
	rekey=no
	left=%any
	leftid=%any
	leftcert=server-cert.pem
	leftsendcert=always
	leftsubnet=0.0.0.0/0
	right=%any
	rightid=%any
	rightauth=eap-mschapv2
	rightsourceip=10.10.10.0/24
	rightdns=8.8.8.8,8.8.4.4
	rightsendcert=never
	eap_identity=%identity
	ike=chacha20poly1305-sha512-curve25519-prfsha512,aes256gcm16-sha384-prfsha384-ecp384,aes256-sha1-modp1024,aes128-sha1-modp1024,3des-sha1-modp1024!
	esp=chacha20poly1305-sha512,aes256gcm16-ecp384,aes256-sha256,aes256-sha1,3des-sha1!
```
- `uniqueids` &ndash; enable (`never`) or disable (`replace`) multiple connections from the same user;
- `right` &ndash; accept incoming connections from any or specific IP address;
- `rightdns` &ndash; DNS servers to be used.

## 4. Adding VPN users
Open configuration file `/etc/ipsec.secrets` and add new users:

```shell
: RSA "server-key.pem"
username : EAP "password"
```

## 5. Reloading VPN configuration
```shell
service strongswan-starter start
```
```shell
ipsec reload
```

## 6. Configuring firewall (iptables)

### 6.1 Allow VPN connections
```shell
iptables -A INPUT -p udp --dport 500 -j ACCEPT
```
```shell
iptables -A INPUT -p udp --dport 4500 -j ACCEPT
```
```shell
iptables -A FORWARD --match policy --pol ipsec --dir in --proto esp -s 10.10.10.10/24 -j ACCEPT
```
```shell
iptables -A FORWARD --match policy --pol ipsec --dir out --proto esp -d 10.10.10.10/24 -j ACCEPT
```
```shell
iptables -t nat -A POSTROUTING -s 10.10.10.10/24 -o eth0 -m policy --pol ipsec --dir out -j ACCEPT
```
```shell
iptables -t nat -A POSTROUTING -s 10.10.10.10/24 -o eth0 -j MASQUERADE
```

### 6.2 Save firewall rules
```shell
iptables-save
```

### 6.3 Save firewall rules permanently
```shell
netfilter-persistent save
```

## 7. Configuring network

### 7.1 Enable network options
Open configuration file `/etc/sysctl.conf` and uncomment the next lines:
```
net.ipv4.ip_forward=1
net.ipv4.conf.all.accept_redirects=0
net.ipv4.conf.all.send_redirects=0
```

### 7.2 Apply the changes
```shell
sysctl -p
```

## 8. Scheduling VPN restart
### 8.1 Open crontab
```shell
crontab -e
```

### 8.2 Add a new cron task (restart every day at `04:00`)
```
0 4 * * * ipsec restart
```
