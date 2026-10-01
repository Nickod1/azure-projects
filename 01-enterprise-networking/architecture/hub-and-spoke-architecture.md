# Architecture

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
