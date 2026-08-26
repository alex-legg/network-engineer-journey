# Network Engineer Journey

An evolving GNS3-based enterprise network demonstrating progressively more advanced networking concepts, from basic routing and VLAN segmentation through to dynamic routing, network services, security, and troubleshooting.

## Phase 1.1 - A startup needs you!

### Objective
I'm joining a startup with five employees to help design, configure, and build a network with limited resources. Their only requirements are that it provide reliable internet connectivity while keeping the network simple, practical, and easy to maintain.

### Topology
I'll be using a SOHO network architecture consisting of PC end devices, a Layer 2 unmanaged switch, and a FRR router. I won't be using a SOHO router that combines routing, switching, and wireless AP functions because I need 5 Ethernet ports for my end devices, and these devices typically provide only 4 usable LAN switch ports. 

The NAT device is included to give my GNS3 network internet access through my host computer. This simulates an office with an ISP connection.

A /29 subnet provides 6 usable host addresses, which is one more than I need. However, I’ll use a /28 subnet, providing 14 usable host addresses, as it offers greater scalability.

The router connected to the mgmt-pc will use a /30 subnet, as it's a point-to-point link requiring only two usable addresses.

I added a Linux PC to test SSH/IP connectivity to the router, as the VPCS hosts don't support SSH.

![Topology](Phase%201.1%20-%20A%20startup%20needs%20you%21/screenshots/Topology.png)

### Technologies
Ethernet, IPv4, Ethernet switching, access ports, ICMP, ARP, IP routing, network switches, routers, and Linux.

### Implementation
All implementations described below have been applied to rtr-hq01-edge01 unless stated otherwise.
#### Pre-network and post-connectivity hardening
I hardened before connecting to the internet.
##### Identity
- Updated hostname in /etc/hostname (unique, reflecting role/location)
- Updated /etc/hosts to bind new hostname with loopback address
- Added Linux user [admin]
- Updated mgmt-pc hostname
- Updated VPCS hosts hostnames
- Mapped domain name to IP in /etc/hosts file. As there is currently no DNS server, I manually added that mapping on the mgmt-pc
##### Network configuration
- Configured a persistent static IP address on eth1 in /etc/network/interfaces, and added a comment to the file, and a description to the interface in VTYSH
- Configured a persistent loopback address in /etc/network/interfaces
- Configured persistent static IP addressing for enp2s0 on mgmt-pc
- Configured the router to connect to the GNS3 NAT node
- Configured IP address, subnet mask, and default gateway on VPCS hosts
##### SSH hardening
- Backed up the SSH directory
- Configured sshd to only allow admin user SSH access (not root), disabled password authentication, and mapped ListenAddress to inside interface on point-to-point link to mgmt-pc
- Generated an Ed25519 SSH key pair on mgmt-pc and added the public key to authorized_keys in the admin user's home directory on the router
- Ensured the correct ownership and permissions are applied to the .ssh directory and authorized_keys file
##### Remote management
- Generated SSH host keys, and confirmed SSH uses the secure version 2
##### Services and attack surface
- Disabled unnecessary protocols/services (ripd | ripngd | isisd) within /etc/frr/daemons
- Shut down all unused ports/interfaces for security, added the comment SPARE to interfaces in /etc/network/interfaces, and added descriptions to interfaces in FRR VTYSH
- Added privileged EXEC authentication
- Prevented plaintext passwords from being displayed in the configuration files
##### Configuration management
- Saved the running configuration and performed a local backup of the entire FRR directory
- Used SFTP on mgmt-pc to pull the frr backup from rtr-hq01-edge01

### Problems
- Having only worked with simulation software where I never run out of switch ports as the devices commonly have 24 or 48, I overlooked the fact that this startup company won't need that many, and that a 5-port or 8-port switch would be more cost-effective. The 5-port caught my eye as that's the number of end devices I have, until I realised I need one more port for the uplink to the router.
- PC-HQ01-VLAN01-01 was my ideal PC hostname, but I'm limited to 12 characters. Now If I add a VLAN above 9 and more than 9 PC's I'll need a new naming convention so I better come up with a future-proof one now.
- In FRR vtysh, the hostname command failed to persist because it inherits its hostname from the underlying Linux OS hostname.
- There's no equivalent of adding a description to a port/interface in the Linux Shell. The recommended approach is to add a comment above the interface in the /etc/network/interfaces file. As the unused interfaces are already absent from the configuration file, I'll consider that sufficient.
- Used `service password-encryption` to prevent plaintext passwords from being displayed in the configuration files. Upon verification I notice it's duplicated itself many times in the running-config, including the passwords it's encrypted. I found out from researching online that this is a bug in some versions of FRR that's mostly "cosmetic and configuration bloat". An upgrade would likely solve the problem, but I can't do that in this GNS3 project right now.
- A directory has to be bundled into one file and compressed before transferred.
- I configured IP information on the router's outside interface to enable communication with external networks. Pings were unsuccessful. I then discovered that the NAT node runs a built-in DHCP server that will assign an IP configuration to the router. I then enabled the router interface and configured it to obtain its IP configuration via DHCP. Applied the network configuration using `ifdown eth0 && ifup eth0`. The router successfully received its IP configuration from the NAT node. Finally, verified external connectivity by successfully pinging Google's DNS server at 8.8.8.8.
- Tested enable password - VTYSH access didn't prompt for the password, I tried various connections. It's in the running-config and encrypted, but I can still freely access privileged exec mode.

### Troubleshooting
- To ensure the FRR hostname remained permanent I updated the hostname in RAM, hostname in /etc/hostname, and wrote the vtysh running-config to startup-config. I checked the running-config, and both the old hostname and new hostname were visible. On a test environment I ran `service frr restart`, and the old hostname was removed.
- Listed running processes with `ps aux`, identified unnecessary FRR routing daemons (ripd, ripngd, and isisd) and disabled them in the FRR configuration. Restarted FRR, then verified with `ps aux` that the daemons were no longer running.
- Verified all interfaces were shut down with `ip link show`.
- Verified that descriptions had been added to the interfaces using `show interface description` and `show running-config`.
- Verified that the loopback and eth1 interfaces are up, descriptions added, and IP connectivity confirmed using ping.
- Verified mgmt-pc can ping rtr-hq01-edge01.
- Successfully connected to rtr-hq01-edge01 via SSH. Connecting via a non-root user is recommended because it limits the damage if the SSH credentials are compromised.
- Verified mgmt-pc can ping rtr-hq01-edge01 using its domain name.
- SSH hardening went smoothly. Verified that password authentication on server is disabled by attempting an SSH connection with a test user that had no authorised key; there was no fallback prompting me for the admins password. Also verified that root SSH access from mgmt-pc is disabled, and that admin is the only permitted user. Finally, verified that SSH connections can only be made via the internal network to mgmt-pc, as the SSH ListenAddress is bound to the inside interface.
- Verified IP connectivity from the VPCS hosts.

### Lessons learned
- I should select hardware based on business requirements.
- When ordering switches, don't forget to account for the port used to connect the switch to the router. Though many modern 24-48 port switches include extra ports for uplinks.
- FRR provides the routing control-plane functionality, while the Linux kernel provides the underlying network interfaces, packet forwarding, and networking stack.
- Used traceroute to examine the path taken by packets from the GNS3 router to Google's DNS server: router outside interface > GNS3 NAT node (virtual network) > home router > ISP > further Internet hops > 8.8.8.8.
- The enable password in VTYSH is generally considered redundant and obsolete, although it has some uses. Instead of relying on enable password, secure FRR using Linux security mechanisms.
- In sshd_config I can specify which IP address/interface SSH accepts incoming connections on, allowing me to restrict access to my internal network or a specific VPN interface.
