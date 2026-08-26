# Addressing Plan

## nat-hq01-edge01
- nat0  192.168.122.1   255.255.255.0

## rtr-hq01-edge01
- Loopback0 192.168.255.1
- eth0      192.168.122.115 255.255.255.0   rtr-hq01-edge01 to gns3-nat eth0 NAT (Internet)
- eth1      192.168.1.1     255.255.255.240 rtr-hq01-edge01 to sw-hq01-acc01 eth1 UPLINK (Prod)
- eth2      192.168.1.17    255.255.255.252 rtr-hq01-edge01 to mgmt-pc eth2 UPLINK (Prod)

## mgmt-pc
- enp2s0 192.168.1.18   255.255.255.252

## VPCS's
- HQ01-V1-01    192.168.1.2 255.255.255.240
- HQ01-V1-02    192.168.1.3 255.255.255.240
- HQ01-V1-03    192.168.1.4 255.255.255.240
- HQ01-V1-04    192.168.1.5 255.255.255.240
- HQ01-V1-05    192.168.1.6 255.255.255.240
