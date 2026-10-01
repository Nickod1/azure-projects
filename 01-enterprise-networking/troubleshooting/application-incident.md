# **Application Connectivity Testing**

## Symptom

The Production web VM could no longer connect to the application
server on TCP/8080.

## Initial Hypothesis

Possible causes included:

- DNS
- Routing
- NSG configuration
- Application availability

## Investigation

DNS resolution was validated first.

Connectivity to the destination IP was then tested.

Effective routes were inspected.

Azure Network Watcher IP Flow Verify showed that TCP/8080
was being denied.

## Root Cause

An NSG rule prevented traffic from the Web subnet to the
Application subnet on TCP/8080.

## Resolution

The NSG rule was corrected to permit the required traffic.

## Validation

Connectivity was retested:

```bash
curl http://app01.internal.dedocoton.local:8080
```



## **Application Connectivity Testing**
A simple HTTP service was deployed on the application VM:
```
python3 -m http.server 8080
```
Connectivity was tested from the web VM:
```
curl http://10.20.2.4:8080
```
This provided a simple method of validating:

* VNet connectivity
* Routing
* NSG rules
* TCP connectivity
* Application availability

The test was repeated after intentionally modifying NSG rules to confirm that network security controls were functioning as expected.

## **Azure Routing**
Azure effective routes were inspected to understand how traffic was being forwarded between resources.

The lab also introduced User Defined Routes and route tables.

A route table was associated with the Production application subnet to explore how custom routes can override Azure's default routing behavior.

A future centralized firewall architecture could use a route such as:
```
0.0.0.0/0
    |
    v
Virtual Appliance
    |
    v
Azure Firewall
    |
    v
Internet
```
This would allow outbound traffic to be inspected through a centralized security layer.

Azure Firewall itself was not required to remain deployed for this lab because of the cost associated with running the service.
