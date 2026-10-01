## **Overview**

This project is part of my hands-on Azure Cloud Engineering learning path. The objective was to design, deploy, secure, and troubleshoot a segmented Azure network using a hub-and-spoke architecture.

Rather than focusing only on deploying Azure resources, this lab was designed to develop practical cloud engineering skills including network design, traffic segmentation, private administration, DNS, routing, Network Security Groups (NSGs), and systematic troubleshooting.

## **Project Objectives**

The main objectives of this project were to:

* Design an Azure IP addressing strategy.
* Build a hub-and-spoke network architecture.
* Separate production and development environments.
* Configure VNet peering.
* Deploy workloads without public IP addresses.
* Use Azure Bastion for secure administrative access.
* Implement network segmentation using NSGs.
* Configure private DNS resolution.
* Understand Azure routing and User Defined Routes (UDRs).
* Use Azure Network Watcher for troubleshooting.
* Simulate and diagnose networking failures.

## **Architecture**

The environment consists of three Azure Virtual Networks:

Detailed addressing information is available in the
[Architecture Diagram](archtecture/network-architecture.png)

Production and Development are connected to the hub through VNet peering.

The spoke VNets are not directly peered with each other.

This design provides centralized connectivity while maintaining logical separation between environments.

## **IP Addressing Plan**

Detailed addressing information is available in the
[IP Addressing Plan](docs/ip-addressing.md).






## **Private DNS**
An Azure Private DNS zone was configured:

```
internal.dedocoton.local
```
The zone was linked to the Production VNet.

A DNS record was created for the application server:
```
app01.internal.dedocoton.local
```
The Production web VM could then access the application using its hostname rather than its IP address:
```
nslookup app01.internal.dedocoton.local
```
Followed by: 
```
curl http://app01.internal.dedocoton.local:8080
```
This demonstrated how private DNS can provide internal name resolution for Azure workloads.

## **Network Troubleshooting**
A major objective of this project was learning how to troubleshoot Azure networking systematically instead of changing configuration until connectivity begins working.

My troubleshooting process was:
```
Connectivity Problem
        |
        v
DNS Resolution
        |
        v
Source Configuration
        |
        v
Effective Routes
        |
        v
NSG Rules
        |
        v
(Firewall / NVA)
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
Azure Network Watcher and operating-system networking tools were used during troubleshooting.

Tools included:
```
Azure Network Watcher
Connection Troubleshoot
IP Flow Verify
Effective Security Rules
Effective Routes

Linux:
ip addr
ip route
nslookup
curl
ss -tulpn
```

## **Failure Simulation**

Several failures were intentionally introduced into the environment to practice troubleshooting.

### **Incident 1: NSG Misconfiguration**

An NSG rule was modified to prevent:
```
Web --> Application TCP/8080
```
Instead of immediately reviewing the NSG, troubleshooting started from the client and followed the network path.

The investigation confirmed that DNS and routing were functioning correctly before the effective security rules identified the blocked traffic.

### **Lesson Learned**

A connectivity problem should not automatically be treated as an application problem. Network security controls need to be validated as part of the troubleshooting process.

### **Incident 2: DNS Failure**

The DNS record for:
```
app01.internal.dedocoton.local
```

was intentionally changed to an incorrect IP address.

Access using the hostname failed while direct communication with the correct IP address continued working.

This isolated the issue to name resolution rather than routing or application availability.

### **Lesson Learned**

When:
```
Hostname fails
```
but:
```
IP address works
```
DNS should be one of the first components investigated.

### **Incident 3: Application Failure**

The HTTP service running on TCP/8080 was stopped.

Network connectivity remained functional, but requests to the application failed.

The following commands were used to verify whether the service was listening:
```
ss -tulpn
```
and
```
curl localhost:8080
```

### **Lesson Learned**
Successful network connectivity does not guarantee application availability. The destination service must also be running and listening on the expected port.

### **Incident 4: Routing Failure**
An incorrect User Defined Route was introduced.

Effective Routes were inspected to identify the unexpected traffic path.

The incorrect route was removed and normal connectivity restored.

### **Lesson Learned**

Effective Routes are an important troubleshooting tool because the configured route is not always enough to understand the actual path Azure selects.

### **NSG vs Azure Firewall**

An NSG provides network-layer filtering for Azure resources and subnets using source/destination addresses, protocols and ports.

Azure Firewall provides centralized network security and traffic inspection capabilities for larger environments.

Conceptually:
```
NSG
    -> Local subnet/NIC traffic filtering

Azure Firewall
    -> Centralized traffic inspection and control
```
They can complement one another rather than necessarily replacing one another.

## **Key Skills Practiced**

This project provided hands-on experience with:

* Azure Virtual Networks
* Subnet design
* CIDR planning
* Hub-and-spoke architecture
* VNet peering
* Network Security Groups
* Azure Bastion
* Private virtual machines
* Azure routing
* User Defined Routes
* Azure Private DNS
* Azure Network Watcher
* Effective routes
* Effective security rules
* Linux networking tools
* Network segmentation
* Least-privilege network access
* Azure troubleshooting

## **Architecture Decisions**

### **Why Hub-and-Spoke?**

Hub-and-spoke provides a scalable way to centralize shared connectivity and security services while maintaining separation between workloads and environments.

As the environment grows, services such as firewalls, VPN gateways, Bastion and DNS infrastructure can be centralized in the hub.

### **Why No Public IPs on Workload VMs?**

Removing public IP addresses reduces the externally exposed attack surface.

Administrative connectivity can instead be provided through controlled services such as Azure Bastion.

**Why Separate Production and Development?**

Separating environments reduces the possibility that development activity will directly affect production workloads and provides clearer security and governance boundaries.

### **Why NSGs?**

NSGs provide subnet/NIC-level traffic filtering and allow application tiers to communicate according to defined network requirements.

## **What I Would Improve for Production**

This lab intentionally used a simplified architecture to control cost and focus on networking fundamentals.

A production implementation could add:

* Azure Firewall
* NAT Gateway
* VPN Gateway or ExpressRoute
* Centralized Private DNS
* Azure Application Gateway/WAF
* DDoS Network Protection where justified
* Azure Policy
* Microsoft Defender for Cloud
* Centralized Log Analytics
* Network monitoring and alerting
* Infrastructure as Code
* CI/CD deployment pipelines

The next stage of this project will begin addressing one of the largest limitations of the current deployment: **manual infrastructure provisioning.**

## **Next Step: Infrastructure as Code**
The Azure networking environment will be rebuilt using Terraform.

The objective will be to move from:
```
Engineer
   |
Azure Portal
   |
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
The infrastructure will be represented as version-controlled code using reusable modules, variables, outputs and remote Terraform state.

This will make the environment reproducible and provide a foundation for automated CI/CD infrastructure deployments later in the project.
