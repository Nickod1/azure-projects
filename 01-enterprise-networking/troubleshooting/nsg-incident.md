# NSG Connectivity Incident

## Symptom

The Production web VM was unable to connect to the application
VM on TCP port 8080.

## Expected Behaviour

The Web subnet should be able to communicate with the
Application subnet on TCP/8080.

## Initial Hypotheses

Possible causes included:

- DNS resolution
- Incorrect routing
- NSG configuration
- Host firewall
- Application service unavailable

## Investigation

DNS resolution was validated first.

The destination IP address was then tested directly to separate
DNS from network connectivity.

Azure Effective Routes were inspected to verify the expected
network path.

Network Watcher and Effective Security Rules were then used to
evaluate TCP/8080 between the source and destination.

## Root Cause

An NSG rule was blocking TCP/8080 between the Web and
Application subnets.

## Resolution

The NSG configuration was corrected to permit the required
traffic.

## Validation

Connectivity was retested:

```bash
curl http://app01.internal.dedocoton.local:8080
