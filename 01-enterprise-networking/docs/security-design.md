## **Secure Administrative Access**
The virtual machines deployed during the project were configured without public IP addresses.

Administrative access was provided through Azure Bastion.
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
This reduces direct Internet exposure because SSH or RDP does not need to be publicly accessible on each workload. As shown in the screenshot, only the bastion VM has a public IP.
<img width="1444" height="349" alt="image" src="https://github.com/user-attachments/assets/d0c208ef-7133-4dd7-ad12-49674c4b0eea" />


## **Network Security**
Network Security Groups were used to segment the Production environment.

The application architecture was designed around three tiers:
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
The intended security policy was:
|Source      |Destination      |Port                |Result |
|------      |-----------      |----                |------ |
|Web         |Application      |TCP/8080            |Allow  |
|Application |Database         |TCP/1433            |Allow  |
|Web         |Database         |N/A                 |Deny   |
|Internet    |Private workloads|Administrative ports|Deny   |

The goal was to follow the principle of least-privilege where each application tier can communicate only with the services it requires.

NSG effective security rules were reviewed during troubleshooting to verify how Azure evaluated traffic.

**nsg-prod-app | Inbound security rules**
<img width="1126" height="412" alt="image" src="https://github.com/user-attachments/assets/62eb45c0-03ab-404a-8a4e-09c20a04a688" />

**nsg-prod-app | Outbound security rules**
<img width="1126" height="412" alt="image" src="https://github.com/user-attachments/assets/fccbcd24-eb89-42c2-9d41-f1a0c481fc37" />

**nsg-prod-data | Inbound security rules**
<img width="1126" height="412" alt="image" src="https://github.com/user-attachments/assets/c59c6fb7-05d6-4ecf-8ae5-a6bf1798165c" />

**nsg-prod-data | Outbound security rules**
<img width="1126" height="412" alt="image" src="https://github.com/user-attachments/assets/a6491eca-0718-4c39-bf8c-4f3353923db7" />
