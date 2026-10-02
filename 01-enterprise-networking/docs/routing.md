# Azure Routing Design

## Default Routing

Azure automatically creates system routes for each subnet.

The lab examined how these routes provide connectivity to:

- Other addresses within the VNet
- Peered VNets
- Azure services
- External destinations

## VNet Peering

Hub ↔ Production

Hub ↔ Development

Production and Development are not directly peered.

VNet peering is non-transitive.

## User Defined Routes

A route table was introduced to demonstrate how UDRs can
influence Azure traffic paths.

A future centralized security architecture could use:

0.0.0.0/0
        ↓
Virtual Appliance
        ↓
Azure Firewall

## Troubleshooting

Effective Routes were used to identify the routes Azure actually applied to network interfaces.

This was particularly useful during the intentionally introduced routing failure.
