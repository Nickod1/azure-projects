## **Hub-and-Spoke Networking**

The Hub VNet acts as the central connectivity point for the environment.

The Production and Development VNets operate as spokes.
```
Production
    |
    |
   Hub
    |
    |
Development
```
<img width="641" height="616" alt="image" src="https://github.com/user-attachments/assets/5ee2c9a1-be68-43c1-a806-35ebc5553305" />

VNet peering was configured between:
```
Hub <--> Production
Hub <--> Development
```
<img width="1100" height="408" alt="image" src="https://github.com/user-attachments/assets/7e3e9967-9f63-4440-a1ee-41d91cd4474e" />

There is no direct Production-to-Development peering.

One important concept demonstrated during the lab was that Azure VNet peering is non-transitive.

For example:
```
Production --> Hub
```
and:
```
Hub --> Development
```
does not automatically provide:
```
Production --> Hub --> Development
```
Additional routing and a network virtual appliance or Azure Firewall would be required if traffic needed to transit the hub.
