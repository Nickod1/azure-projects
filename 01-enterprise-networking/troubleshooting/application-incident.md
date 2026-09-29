# NSG Connectivity Incident

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
