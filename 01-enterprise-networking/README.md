# Azure Enterprise Networking

## Overview

This project demonstrates the design, deployment, security, validation, and troubleshooting of a segmented Azure network using a hub-and-spoke architecture.

The environment simulates an enterprise Azure deployment with separate Production and Development networks, centralized administrative access through Azure Bastion, network segmentation using Network Security Groups (NSGs), private DNS resolution, custom routing, and Azure-native network troubleshooting tools.

The project was designed to go beyond basic resource deployment by focusing on the reasoning behind the architecture, traffic flows, security controls, and systematic troubleshooting.

## Architecture
Detailed network diagram is available in the
[Azure Hub-and-Spoke Architecture](architecture/network-architecture.png).

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
