# Naming Convention

## Hostnames
Device | Location | Role/ID
`nat-hq01-edge01`
`rtr-hq01-edge01`
`sw-hq01-acc01`

Location | VLAN | ID
`HQ01-V1-01`
`HQ01-V1-02`
`HQ01-V1-03`
`HQ01-V1-04`
`HQ01-V1-05`

Management workstation
`mgmt-pc`

## Interface descriptions
Edge router to access switch
`rtr-hq01-edge01 to sw-hq01-acc01 eth1 UPLINK (Prod)`
Edge router to GNS3 NAT node
`rtr-hq01-edge01 to gns3-nat eth0 NAT (Internet)`

## Backup configurations
`/var/backups/frr/frr_backup_2026-08-23`
`/var/backups/ssh/ssh_backup_2026-08-24`
