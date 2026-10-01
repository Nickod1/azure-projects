# Architecture

<img width="789" height="981" alt="network-architecture" src="https://github.com/user-attachments/assets/2a5d7e82-2165-4acc-90a1-a13576661117" />

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
[IP Addressing](docs/ip-addressing.md)
