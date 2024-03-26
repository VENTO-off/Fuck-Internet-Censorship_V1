# IKEv2 VPN configuration guide

## 1. Windows configuration
### 1.1 Save certificate locally
Save the certificate to a local file named `ca-cert.crt`.
```shell
cat /etc/ipsec.d/cacerts/ca-cert.pem
```

### 1.2 Import certificate into Windows
Run file `ca-cert.crt` &rarr; `Install Certificate...` &rarr; `Local Machine` &rarr; `Browse...` &rarr; `Trusted Root Certification Authorities` &rarr; `Finish`.

### 1.3 Create a new VPN connection
Go to `Settings` &rarr; `Network & Internet` &rarr; `VPN` &rarr; `Add a VPN connection`.

### 1.4 Fill connection information
| Parameter name         | Description                                 |
|------------------------|---------------------------------------------|
| VPN provider           | Windows (built-in)                          | 
| Connection name        | Any VPN connection name                     |
| Server name or address | VPN IP address                              |
| VPN type               | `IKEv2`                                     |
| Type of sign-in info   | `Username and password`                     |
| User name              | Username from the file `/etc/ipsec.secrets` |
| Password               | Password from the file `/etc/ipsec.secrets` |

Press `Save` button to save settings.

### 1.5 Connect to the VPN
Select your VPN configuration and press `Connect` button.
> **Note!** You can also connect to the VPN server in `Internet access` icon in the taskbar.
