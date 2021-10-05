# cfl-vpn-ap

Provisions a WiFi access point that routes clients' traffic over a VPN service.

## Requirements

* A Debian-based Linux instance that has both a wired and wireless network interface.
* A [NordVPN](https://nordvpn.com) or [VyprVPN](https://www.vyprvpn.com) account
* The name of the Nord or Vypr VPN configuration (i.e., server) that you would like to use. *See example playbooks, below.*

A [Raspberry Pi](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/) is a great option for this. We have only tested on [Raspberry Pi OS](https://www.raspberrypi.org/software/) but other modern Debian-based OSes may also work.

## Role Variables

| Variable    | Description | Default |
| ----------- | ----------- | ------- |
| ap_dhcp_network | The first three octets of the AP's network. | 192.168.220 |
| ap_dhcp_lease_time | The DHCP lease time of the AP's clients. | 12h |
| ap_dhcp_dns_server | The DNS server assigned to the AP's clients. | 1.1.1.1 |
| ap_channel | The AP's wireless channel. | 6 |
| ap_interface | The name of the AP's (obviously wireless) interface. | wlan0 |
| ap_ssid | The SSID (network name) advertised by the AP. | private |
|***ap_wpa_passphrase** | The AP's WPA passphrase that clients must use to connect.  Must be 8 to 63 printable ASCII characters. | (none) |
| no_vpn_dest_addresses | A list of IP address destinations that should NOT be routed over the VPN. | [ ]|
| no_vpn_dest_hostnames | A list of hostname destinations that should NOT be routed over the VPN. | [ ]|
| ***vpn_username** | The VPN service account's username. | (none) |
| ***vpn_password** | The VPN service account's password. | (none) |
| ***vpn_service** | The VPN service to be used; must be "nord" or "vypr". | (none) |
| ***vpn_name** | The name of the VPN configuration (sometimes called "server") to use. *See example playbooks below.* | (none) |
| vpn_nord_transport | The protocol used by NordVPN | tcp |
| vpn_vypr_bits | The VPN encryption level used for VyprVPN; must be 160 or 256. | 256 |

***required**

## Dependencies

### Collections
* [community.general.iptables_state](https://docs.ansible.com/ansible/latest/collections/community/general/iptables_state_module.html)

### Required variables
* ap_wpa_passphrase
* vpn_username
* vpn_password
* vpn_service
* vpn_name

## Example Playbooks

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

### A minimal example using NordVPN

Please see optional variables and their defaults, above.

```
- hosts: servers
  roles:
    - role: cfl-vpn-ap
      vars:
        ap_wpa_passphrase: supersecretpassword
        vpn_service: nord
        vpn_name: us6917 # New York City
        #vpn_name: nl-onion4 # ingress Netherlands, egress tor
        vpn_username: jdoe@example.com
        vpn_password: af8a0c58fbf4045112de64604190bed3
```

Find the name of the server (`vpn_name`) that you would like to use by visiting NordVPN's tool: [https://nordvpn.com/servers/tools/](https://nordvpn.com/servers/tools/).  Click "Show Advanced Options" to find servers in other geographical areas or with specific features. Note that **"Obfuscated Servers" are NOT supported by this Ansible role at this time** because that requires downloading the openvpn source, applying a 3rd party patch, and compiling and installing a custom build. That is something we may add later, but currently this role simply uses apt to install the OS's supported openvpn package.

### A minimal example using VyprVPN

Please see optional variables and their defaults, above.

```
- hosts: servers
  roles:
    - role: cfl-vpn-ap
      vars:
        ap_wpa_passphrase: supersecretpassword
        vpn_service: vypr
        vpn_name: USA - San Francisco
        #vpn_name: USA - New York
        vpn_username: jdoe@example.com
        vpn_password: d58403c7f0e9ea96c8971a7f4d80fa74
```

Find the name of the server (`vpn_name`) that you would like to use by visiting VyprVPN's Server Locations page:
[https://www.vyprvpn.com/server-locations](https://www.vyprvpn.com/server-locations).

## License

[MIT](https://en.wikipedia.org/wiki/MIT_License)

## Author Information

This Ansible role was created and is maintained by [CofenseLabs](https://cofenselabs.com).
