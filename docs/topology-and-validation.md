# Topology and implementation notes

## Confirm before changing routes

The console screenshots identify the two VPC IDs and the active peering connection, but the full CIDR values are not legible in the VPC inventory screenshot. Confirm these in the AWS console:

- VPC A CIDR
- VPC B CIDR
- Source subnet ID and its associated route table
- Destination subnet ID and its associated route table
- Destination EC2 private IPv4 address

Do not use illustrative CIDRs from generic tutorials. Use the actual values in this AWS account.

## Route logic

Source-to-destination traffic:

`VPC A subnet route table: VPC B CIDR -> pcx-04204cdeeeef8d45d3`

Return traffic:

`VPC B subnet route table: VPC A CIDR -> pcx-04204cdeeeef8d45d3`

Both directions are required. An Active peering status means the connection was accepted; it does not automatically add routes or security-group permissions.

## Public subnet versus private subnet

The source instance's public subnet normally has a default route to an Internet Gateway. The destination subnet can remain private. Peering traffic uses private IP routing and the peering routes, not the Internet Gateway or NAT Gateway.

A NAT Gateway provides private-subnet-initiated internet access. It is not required for VPC peering traffic.

## Validation

From the source EC2 instance, test the destination's private IP:

```bash
ping -c 4 <VPC_B_EC2_PRIVATE_IP>
nc -zv <VPC_B_EC2_PRIVATE_IP> 22
```

A successful ping to 8.8.8.8 proves public internet reachability only. It does not prove VPC peering.

If private-IP connectivity fails, verify non-overlapping CIDRs, both route-table entries and subnet associations, security-group rules, Network ACL request/return rules, destination instance state, and the operating-system firewall.
