# IP Addressing Plan

| Environment | Subnet | CIDR |
|---|---|---|
| Hub | AzureBastionSubnet | 10.10.1.0/26 |
| Hub | ManagementSubnet | 10.10.2.0/24 |
| Hub | AzureFirewallSubnet | 10.10.3.0/26 |
| Production | snet-prod-web | 10.20.1.0/24 |
| Production | snet-prod-app | 10.20.2.0/24 |
| Production | snet-prod-data | 10.20.3.0/24 |

The /16 VNet ranges provide room for future subnet expansion while the /24 workload subnets provide sufficient address capacity for the current environment.

Non-overlapping address spaces were used to support VNet peering and simplify future connectivity.
