# Azure Enterprise Networking

## Overview

This project demonstrates the design, deployment, security, validation, and troubleshooting of a segmented Azure network using a hub-and-spoke architecture.

The environment simulates an enterprise Azure deployment with separate Production and Development networks, centralized administrative access through Azure Bastion, network segmentation using Network Security Groups (NSGs), private DNS resolution, custom routing, and Azure-native network troubleshooting tools.

The project was designed to go beyond basic resource deployment by focusing on the reasoning behind the architecture, traffic flows, security controls, and systematic troubleshooting.


## Architecture
[Azure Hub-and-Spoke Architecture](architecture/network-architecture.png)

The environment consists of three Azure Virtual Networks:

- **Hub VNet:** `10.10.0.0/16`
- **Production VNet:** `10.20.0.0/16`
- **Development VNet:** `10.30.0.0/16`

The Production and Development VNets are connected to the Hub using VNet peering.

There is intentionally no direct peering between Production and Development.

The Hub provides a centralized location for shared connectivity and management services such as Azure Bastion and provides space for future services such as Azure Firewall or VPN Gateway.


## Technologies Used

### Microsoft Azure

- Azure Virtual Networks
- Subnets
- VNet Peering
- Network Security Groups
- Azure Bastion
- Azure Private DNS
- Route Tables
- User Defined Routes
- Azure Network Watcher
- Azure Virtual Machines

### Networking

- TCP/IP
- CIDR and subnetting
- DNS
- Routing
- Network segmentation
- Hub-and-spoke architecture
- Least-privilege network access

### Operating System & Tools

- Linux
- SSH
- Azure CLI
- Azure Portal
- Git
- GitHub
- `curl`
- `nslookup`
- `ip`
- `ss`


## Network Design

The network uses a hub-and-spoke model:

```text
                   Administrator
                         |
                         v
                  Azure Bastion
                         |
                 +-------+-------+
                 |    HUB VNET   |
                 | 10.10.0.0/16  |
                 +-------+-------+
                         |
                  VNet Peering
                  /           \
                 /             \
                v               v
      +----------------+   +----------------+
      |   PROD VNET    |   |    DEV VNET    |
      | 10.20.0.0/16   |   | 10.30.0.0/16   |
      +----------------+   +----------------+
```

The hub-and-spoke architecture provides centralized connectivity while maintaining logical separation between workloads.
Azure VNet peering is non-transitive. Therefore:
```
Production → Hub
Hub → Development
```
does not automatically create:
```
Production → Hub → Development
```
Transit between spokes would require additional routing and a routing device or service such as Azure Firewall or another Network Virtual Appliance.
Detailed subnet information is available in:

[IP Addressing Plan](docs/ip-addressing.md)

## Secure Administrative Access
Workload virtual machines were deployed without public IP addresses.
Azure Bastion provides administrative connectivity to the private VMs without requiring SSH or RDP to be directly exposed to the Internet.
```
Administrator
      |
      v
Azure Bastion
      |
      v
Hub VNet
      |
      v
VNet Peering
      |
      v
Private VM
```
This design reduces the externally exposed attack surface of the workload environment.

## Network Segmentation
The Production environment follows a three-tier model:
```
WEB
 |
 | TCP/8080
 v
APPLICATION
 |
 | TCP/1433
 v
DATABASE
```
Network Security Groups were used to control communication between these tiers.
The intended traffic policy was:
| Source | Destination | Port | Action |
|---|---|---|---|
| Web | Application | TCP/8080 | Allow |
| Application | Database | TCP/1433 | Allow |
| Web | Database | — | Deny |
| Internet | Private workload administrative ports | — | Deny |

The goal was to implement least-privilege network connectivity rather than allowing unrestricted communication between application tiers.
Detailed security decisions are documented in:

[Security Design](docs/security-design.md)

## Application Connectivity Testing
A lightweight HTTP service was deployed on the Production application VM:
```
python3 -m http.server 8080
```
Connectivity was tested from the Production web VM:
```
curl http://<APP_PRIVATE_IP>:8080
```
This simple application provided a way to validate several components simultaneously:
- Network connectivity
- Azure routing
- NSG rules
- TCP connectivity
- Application availability
The service was also intentionally disrupted during troubleshooting exercises to distinguish application failures from network failures.

## Private DNS
An Azure Private DNS zone was configured:
```
internal.dedocoton.local
```
The zone was linked to the Production VNet.
An internal DNS record was created for the application server:
```
app01.internal.dedocoton.local
```
DNS resolution was validated from the Production web server:
```
nslookup app01.internal.dedocoton.local
```
Application connectivity could then be tested using the hostname:
```
curl http://app01.internal.dedocoton.local:8080
```
This demonstrated how internal workloads can communicate using private DNS names instead of depending directly on IP addresses.

## Routing
Azure effective routes were examined to understand how Azure determines the path traffic takes between resources.

The project also introduced:
- Azure system routes
- VNet peering routes
- Route tables
- User Defined Routes
- Effective routes
- Traffic steering concepts
A custom route table was associated with the Production environment to demonstrate how UDRs can influence traffic paths.

The environment was also designed with future centralized routing in mind:
```
Workload
   |
   v
Route Table
   |
   v
Azure Firewall / NVA
   |
   v
Destination
```
Azure Firewall was not permanently deployed because the lab was intentionally designed to control Azure consumption costs.

More information is available in:

[Routing Design](docs/routing.md)

## Network Validation
The environment was validated using both Azure-native and Linux networking tools.

**Azure**
- Network Watcher
- Connection Troubleshoot
- IP Flow Verify
- Effective Security Rules
- Effective Routes
**Linux**
```
ip addr
ip route
nslookup
curl
ss -tulpn
```
These tools were used together to determine whether failures originated from DNS, routing, security controls, network connectivity, or the application itself.

## Troubleshooting Methodology
One of the primary goals of this project was developing a systematic troubleshooting process.
Instead of immediately modifying firewall or NSG rules when connectivity failed, troubleshooting followed the network path:
```
Connectivity Issue
       |
       v
DNS Resolution
       |
       v
Source Configuration
       |
       v
Routing
       |
       v
NSG Rules
       |
       v
Firewall / NVA
       |
       v
Destination Reachability
       |
       v
Listening Port
       |
       v
Application Health
```
Several failures were intentionally introduced into the environment.

## Troubleshooting Scenarios
| Incident | Failure Introduced | Primary Diagnostic Area |
|---|---|---|
| NSG | TCP/8080 traffic blocked | Effective security rules |
| DNS | Incorrect application DNS record | Name resolution |
| Application | HTTP service stopped | Application/port status |
| Routing | Incorrect UDR | Effective routes |

Detailed incident reports:
- [NSG Connectivity Incident](troubleshooting/nsg-incident.md)
- [DNS Resolution Incident](troubleshooting/dns-incident.md)
- [Application Availability Incident](troubleshooting/application-incident.md)
- [Routing Incident](troubleshooting/routing-incident.md)

## Key Architecture Decisions
**Why Hub-and-Spoke?**

Hub-and-spoke provides a scalable approach for separating workloads while centralizing shared network services.

As the environment grows, the Hub could host services such as:
- Azure Firewall
- VPN Gateway
- DNS infrastructure
- Bastion
- Network Virtual Appliances

**Why Separate Production and Development?**

Separating the environments provides stronger security and operational boundaries and reduces the possibility of development activity directly affecting Production workloads.

**Why No Public IPs on Workload VMs?**

Workload VMs do not require directly exposed administrative interfaces.
Azure Bastion provides controlled administrative access while keeping the workloads privately addressed.

**Why NSGs?**

NSGs provide subnet- and NIC-level Layer 3/Layer 4 traffic filtering.
They allow communication between application tiers to be restricted according to workload requirements.

**NSG vs Azure Firewall**

NSGs provide distributed traffic filtering close to Azure resources.
Azure Firewall provides centralized network security and traffic control capabilities.
The two technologies can complement each other within an enterprise architecture.

## Production Considerations
This environment is intentionally simplified for hands-on learning and cost control.

A production implementation could additionally include:
- Azure Firewall
- NAT Gateway
- VPN Gateway or ExpressRoute
- Application Gateway and Web Application Firewall
- Centralized private DNS architecture
- DDoS Network Protection where appropriate
- Azure Policy
- Microsoft Defender for Cloud
- Centralized Log Analytics
- Network monitoring and alerting
- Infrastructure as Code
- Automated CI/CD deployments
These services would be selected according to actual security, availability, connectivity, performance, and cost requirements rather than added simply because they are available.

## Skills Demonstrated
This project provided practical experience with:
- Azure network architecture
- Hub-and-spoke design
- IP addressing and subnetting
- VNet peering
- Network segmentation
- Network Security Groups
- Azure Bastion
- Private Azure workloads
- Private DNS
- Azure routing
- User Defined Routes
- Azure Network Watcher
- Effective routes
- Effective security rules
- Linux network troubleshooting
- Application connectivity testing
- Failure simulation
- Root-cause analysis
- Technical documentation

## Lessons Learned
The project reinforced that successful cloud networking requires more than establishing connectivity.

Network design must account for segmentation, routing, name resolution, administrative access, security boundaries, scalability, and troubleshooting.
The failure simulations were particularly valuable because similar symptoms can originate from very different layers.

For example:
```
Application unavailable
```
could result from:
```
DNS
Routing
NSG
Firewall
Operating system
Listening port
Application
```
A structured troubleshooting methodology makes it possible to isolate the failing layer before making configuration changes.

## Next Project
02. Infrastructure as Code
The next project will rebuild this Azure environment using Infrastructure as Code.
The goal is to move from:
```
Engineer
   |
   v
Azure Portal
   |
   v
Azure Resources
```
to:
```
Engineer
   |
   v
Git
   |
   v
Terraform
   |
   v
Azure
```
The next phase will introduce:
- Terraform
- Bicep fundamentals
- Reusable modules
- Variables and outputs
- Remote Terraform state
- Configuration drift
- Version-controlled infrastructure

[Return to Azure Projects](../README.md)
