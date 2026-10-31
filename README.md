# AWS VPC Peering: Public Subnet to Private Subnet

A hands-on AWS networking lab demonstrating VPC peering between two VPCs in **US East (N. Virginia), `us-east-1`**.

## Objective

Enable private-IP communication from an EC2 instance in a public subnet in **VPC A** to an EC2 instance in a private subnet in **VPC B**, using an active VPC peering connection.

> **Evidence status:** The screenshots show an active peering connection named `AB peering` and a successful `ping 8.8.8.8` from the VPC A instance. The public-internet ping does **not** prove VPC-to-VPC connectivity. A private-IP test from the source EC2 instance to the destination EC2 instance still needs to be captured.

## Environment observed

| Component | Observed details |
|---|---|
| AWS Region | `us-east-1` |
| VPC A (requester) | `A vpc -vpc`, ID `vpc-0c56abf25a80fbe40` |
| VPC B (accepter) | `Demo-vpc-B-vpc`, ID `vpc-040fcec1a6c395bd1` |
| VPC peering | `AB peering`, ID `pcx-04204cdeeeef8d45d3`, status **Active** |
| Source EC2 | `demo A`, instance ID `i-0cd8084d4d90f3e5b`, private IP `10.1.13.186` |
| Other EC2 instances visible | `Demo A private` (`i-0a533e85b8f48100e`) and `B demo private` (`i-034df842d4812ee45`) |

The screenshots do not expose all CIDR blocks, subnet IDs, destination instance private IP, or the complete route entries. Confirm these in the AWS console before applying routes. Do not assume CIDRs from instance IPs alone.

## Architecture

```text
                         AWS Region: us-east-1

      VPC A (Requester)                         VPC B (Accepter)
      A vpc -vpc                                Demo-vpc-B-vpc
      vpc-0c56abf25a80fbe40                    vpc-040fcec1a6c395bd1
      CIDR: confirm in console                  CIDR: confirm in console

   Public subnet                              Private subnet
   EC2: demo A                                EC2: B demo private
   10.1.13.186                                Destination private IP:
          |                                   confirm in EC2 details
          +------------------+  +---------------------+
                             |  |
                   VPC Peering Connection
                   pcx-04204cdeeeef8d45d3
                   Status: Active
```

VPC peering provides private routing between the VPC CIDR ranges. It does not require the private subnet to have an Internet Gateway, and it does not make the peering connection transitive.

## Implementation checklist

- [x] Two VPCs exist.
- [x] EC2 instances exist in the environment.
- [x] VPC peering connection created.
- [x] Peering connection accepted; status is Active.
- [ ] Confirm the exact CIDR of each VPC.
- [ ] Confirm the exact source and destination subnet associations.
- [ ] Add a route in the source subnet's route table: destination **VPC B CIDR**, target **VPC peering connection**.
- [ ] Add a return route in the destination subnet's route table: destination **VPC A CIDR**, target the **same peering connection**.
- [ ] Update destination EC2 security group inbound rules to allow only the required traffic from the source EC2 private IP or smallest appropriate source range.
- [ ] Verify source outbound rules and both subnet Network ACLs.
- [ ] Test connectivity to the destination EC2 **private IP**.
- [ ] Capture successful private-IP test output.

## Route table configuration

Routes must be added to the route tables actually associated with the two participating subnets. Do not assume that a route table named `public` or `private` is the one being used; verify the subnet association.

| Route table | Destination | Target |
|---|---|---|
| VPC A source subnet route table | VPC B CIDR | `pcx-04204cdeeeef8d45d3` |
| VPC B destination subnet route table | VPC A CIDR | `pcx-04204cdeeeef8d45d3` |

Keep existing `local` and internet/NAT routes. The peering routes are additional routes, not replacements.

## Security and connectivity

For a narrow ICMP test, add an inbound rule to the destination EC2 security group allowing **All ICMP - IPv4** from the source EC2 private IP as a `/32` (or the smallest appropriate source range). Security groups are stateful, but Network ACLs are stateless and must allow both request and response traffic.

For an SSH test, allow TCP `22` from the source EC2 private IP `/32`. Do not open SSH to `0.0.0.0/0`.

## Validation

From the source EC2 instance, run:

```bash
ping -c 4 <VPC_B_EC2_PRIVATE_IP>
nc -zv <VPC_B_EC2_PRIVATE_IP> 22
```

A successful `ping 8.8.8.8` only verifies that the source instance can reach a public IP. It does not validate VPC peering.

If the private-IP test fails, check: peering status; non-overlapping VPC CIDRs; subnet-to-route-table associations; both peering routes; destination security-group rules; both Network ACLs; destination instance state and OS firewall; and that you are testing private IPs.

## Cost and cleanup notes

VPC peering itself has no hourly charge, but data transfer charges may apply. NAT Gateways and running EC2 instances can incur ongoing charges. Delete unused NAT Gateways and stop or terminate lab resources only after confirming they are not used by other workloads.

## Evidence

Screenshots should document the EC2 instances, VPC inventory, NAT Gateway inventory, route-table inventory, active peering connection, and the source EC2 public-internet ping. The latter is not proof of peering connectivity.

## Next improvement

Add a final screenshot showing `ping <VPC_B_EC2_PRIVATE_IP>` succeeding from `demo A` to `B demo private`. That is the key proof that this peering lab works end to end.
