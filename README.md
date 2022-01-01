# DD-WRT Configuration
My home DD-WRT configuration for privacy and security. Documenting so I can remember my preferred settings whenever I update/reset the router.

## Current Router
- [Linksys WRT3200ACM](https://www.linksys.com/us/support-product?pid=01t340000046sOsAAI).

## Current DD-WRT Build
- [v3.0-r47911 std (12/23/21)](https://forum.dd-wrt.com/phpBB2/viewtopic.php?t=331086).

## 3rd-Party Services
- [ProtonVPN](https://protonvpn.com/).

## Additional Reference Materials
- [How to set up ProtonVPN on DD-WRT routers](https://protonvpn.com/support/vpn-router-ddwrt/).
- [How to download ProtonVPN OpenVPN configuration files](https://protonvpn.com/support/vpn-config-download/).
- [What is IKEv2/IPSec?](https://protonvpn.com/support/what-is-ikev2-ipsec/).

# Setup VPN
Settings for configuring ProtonVPN.

### Basic Setup

#### Network Setup

##### Network Address Server Settings (DHCP)
_Set ProtonVPN DNS addresses (UDP)_

- Static DNS 1: `10.8.8.1`
- Use DNSMasq for DNS: `Checked`
- DCHP-Authoritative: `Checked`

##### Time Settings
- Time Zone: `America/Los_Angeles`

**Save and Apply Settings.**

### IPv6

#### IPv6 Support
_Ensure IPv6 is set to disable to make sure no IP leaks occur._

IPv6: `Disabled`

**Save and Apply Settings.**

## Services

### VPN

#### OpenVPN Client
_Set **Start OpenVPN Client** to `Enabled`. Before configuring the OpenVPN service, log into [ProtonVPN](https://account.protonvpn.com/login) and [download](https://protonvpn.com/support/vpn-config-download/), download and open desired *.ovpn config file to fill out the necessary fields below:_

- Server IP/Name : Port:
    - Server IP/Name: `[*.ovpnfileservername].protonvpn.com`, e.g. **is-us-01.protonvpn.com**
    - Port: Value behind the server IP, e.g. `1194` or `443` (Leave unchanged, default is `1194`)
- Tunnel Device: `TUN`
- Tunnel Protocol: `udp`
- Encryption Cipher: `AES-256-CBC`
- Hash Algorithm: `SHA512`
- User Pass Authentication: `Enabled`
    - Username and Password: [`OpenVPN IKEv2 credentials`](https://protonvpn.com/support/what-is-ikev2-ipsec/).
        - How to get OpenVPN IKEv2 credentials: Log into [ProtonVPN account](https://account.protonvpn.com/login) and in the menu navigation on the left, go to **Account > OpenVPN / IKEv2 username**.
        - _Append `+f2` at the end of **username** to use PortonVPN NetShield to block malware, ads, and trackers, e.g. 123456789+f2._
- Advanced Options: `Enabled`
- TLS Cipher: `None`
- Compression: `No`
- NAT: `Enabled`
- Killswitch: `Checked`
- Source routing (PBR): `Route selected sources via VPN`
    - _To enable Policy based Routing that allows certain devices route via VPN._
- Additional config box:
    - ```
        tls-client

        remote-cert-tls server

        remote-random

        nobind

        tun-mtu 1500

        tun-mtu-extra 32

        mssfix 1450

        persist-key

        persist-tun

        ping-timer-rem

        reneg-sec 0

        #log /tmp/vpn.log
- CA Cert: Copy and paste the entire CA Cert from the *.ovpn file. Be sure to include the entire text from `-----BEGIN CERTIFICATE-----` and `-----END CERTIFICATE-----` lines.
- TLS Key: Copy and paste the entire TLS Key from the *.ovpn file. Be sure to include the entire text from `-----BEGIN OpenVPN Static key V1-----` and `-----END OpenVPN Static key V1-----` lines.

**Save and Apply Settings.**

## Verify VPN is working

Go to **Status > OpenVPN**. Under **State**, **Client** should say: `CONNECTED SUCCESS`

# Setup static IPs and include those devices in Policy based Routing
Configure static leases for devices that were assigned dynamic IPs via DHCP for Policy based Routing.

- Go to **Status > LAN > DHCP Clients** to see the list of devices.
- Open a text editor, e.g. Notepad, and copy and paste the name of the devices with their respective MAC and IP addresses.

## Services

### Services

#### DHCP Server

- Static Leases: `Add` the amount of IP addresses desired to assign static leases.
- Input those devices from the text editor into the Static Leases.

**Save and Apply Settings.**

## Services

### VPN

#### OpenVPN Client

- Policy based Routing: Include the static IPs following the below format:
    - `###Device name###`
    - `IP address`

_To stop an IP address from being routed to VPN, comment out the IP address with three # on both sides of the IP, e.g. **###XXX.XXX.X.X####**_

**Save and Apply Settings.**
