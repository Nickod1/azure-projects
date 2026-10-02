# IP Addressing Plan

## Addressing Strategy

Non-overlapping RFC1918 address spaces were allocated to each
environment to support VNet peering and future expansion.

| Environment | VNet / Subnet | CIDR |
|---|---|---|
| Hub | vnet-hub | 10.10.0.0/16 |
| Hub | AzureBastionSubnet | 10.10.1.0/26 |
| Hub | ManagementSubnet | 10.10.2.0/24 |
| Hub | AzureFirewallSubnet | 10.10.3.0/26 |
| Production | vnet-prod | 10.20.0.0/16 |
| Production | snet-prod-web | 10.20.1.0/24 |
| Production | snet-prod-app | 10.20.2.0/24 |
| Production | snet-prod-data | 10.20.3.0/24 |
| Development | vnet-dev | 10.30.0.0/16 |
| Development | snet-dev-web | 10.30.1.0/24 |
| Development | snet-dev-app | 10.30.2.0/24 |

## Design Decision

/16 address spaces were assigned at the VNet level to provide
room for future subnet expansion.

Smaller subnet ranges were then allocated according to workload
requirements.

The VNet address spaces do not overlap, allowing VNet peering
and leaving room for future hybrid connectivity.
