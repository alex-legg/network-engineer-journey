# Commands

## VPCS
Change hostname
`set pcname HQ01-V1-01`
Save current config to startup file
`write` or `save`
Show IPv4 information
`show ip`
Configure IP address, netmask and default gateway
`ip 192.168.1.1/28 192.168.1.254`

## FRR VTYSH
Enter configuration mode
`conf t`
Change hostname
`hostname [hostname]`
Save current config to startup file
`write`
Prevent plaintext passwords from being displayed in the configuration files
`service password-encryption`
Show running-config
`show running-config`
Add description to interface in interface configuration mode
`description SPARE`
Summary of all interfaces
`show interface brief`

## FRR Linux Shell
Add user [u:admin p:11111111], and create home directory
`adduser admin`
Change hostname
`hostname [hostname]` - RAM
`vi /etc/hostname` - make it permanent
`vi /etc/hosts` - bind new hostname to loopback address
Restart FRR
`service frr restart`
Check startup config
`vi /etc/frr/frr.conf`
Show all running processes
`ps aux`
Print system information e.g. version
`cat /etc/os-release`
Edit FRR startup configuration for routing protocol daemons
`vi /etc/frr/daemons`
Show all network interfaces and their link status
`ip link show`
Show the IP configuration of a specific interface
`ip addr show dev eth1`
Verify the loopback interface and its IP address
`ip addr show lo`
Update physical and logical network interface configuration file
`vi /etc/network/interfaces`
Apply new network interface. If working remotely, don't blindly ifdown, I could disconnect myself
`ifdown [int_name] && ifup [int_name]`
Create SSH host keys
`ssh-keygen -A`
Generate SSH key pair with the Ed25519 algorithm
`ssh-keygen -t ed25519`
Backup ssh locally
`cp -a /etc/ssh /var/backups/ssh/ssh_backup_$(date +%Y-%m-%d)`
Create a .tar.gz (c = create archive, z = gzip compress, f = filename)
`tar -czf /var/backups/frr/frr-2026-08-23.tar.gz /var/backups/frr`
Which user am I
`whoami`
Restart sshd
`rc-service sshd reload`
SSH public key setup: create the .ssh directory in the home directory of the account I want to SSH into, then add public key to authorized_keys
`mkdir -p ~/.ssh`
`chmod 700 ~/.ssh`
`vi ~/.ssh/authorized_keys`
`chmod 600 ~/.ssh/authorized_keys`
Ensure .ssh directory and everything inside it is owner:admin, group:admin
`chown -R admin:admin /home/admin/.ssh`

## Debian PC Shell
Activate interface after configuring it
`sudo ifup enp2s0`
SSH to admin (user)
`ssh admin@192.168.1.1`
Pull file using SFTP
`sftp admin@192.168.1.1`
`get /var/backups/frr/frr-2026-08-23.tar.gz`
