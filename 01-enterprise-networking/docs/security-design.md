# Network Security Design

## Security Objectives

The network was designed around the following principles:

- Keep workload VMs privately addressed.
- Avoid direct administrative exposure to the Internet.
- Separate Production and Development.
- Restrict communication between application tiers.
- Permit only required ports and protocols.
- Centralize administrative access.

## Administrative Access

Azure Bastion provides access to private workload VMs.

Workload VMs do not require public IP addresses or publicly
accessible SSH/RDP endpoints.

## Production Segmentation

Production contains three workload tiers:

Web → Application → Database

Required communication:

| Source | Destination | Protocol/Port | Action |
|---|---|---|---|
| Web | Application | TCP/8080 | Allow |
| Application | Database | TCP/1433 | Allow |
| Web | Database | Any | Deny |

## NSG Strategy

NSGs provide Layer 3/Layer 4 filtering at the subnet or network
interface level.

Rules were designed around application requirements rather than
allowing unrestricted communication between Production subnets.

## Future Security Improvements

A production environment could introduce centralized inspection
using Azure Firewall, additional monitoring, Azure Policy,
Defender for Cloud, and appropriate DDoS protections.
