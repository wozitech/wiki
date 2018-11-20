![AWS Logo](/uploads/logos/aws-logo.png "AWS Logo"){.pagelogo}
<!-- TITLE: AWS VPC -->
<!-- SUBTITLE: A quick summary on AWS VPC -->

# The VPC
Virtual Private Cloud (VPC) unique describes the hosted provision of a given customer environment; it's a virtual data centre. Each customer account can have upto five VPC (default; can be increased) and every customer's VPC is unique to that customer, thus providing the separation of each customer's environment.

All VPC use private IP address space:
* 10.0.0.0/8 IP addresses: 10.0.0.0 -- 10.255.255.255
* 172.16.0.0/12 IP addresses: 172.16.0.0 -- 172.31.255.255
* 192.168.0.0/16 IP addresses: 192.168.0.0 – 192.168.255.255

Every account has one VPC by default; DO NOT DELETE THIS DEFAULT VPC.

A VPC can exist in a single region only.

A VPC allows each customer to perform detailed network configuration, restricting and presenting services/resources.

A VPC is typically presented to the Internet by attaching an Internet Gateway (IGW). But it does not have to be, allowing you to create private cloud environments. You can control which aspects of your private cloud environment do have Internet access (for example, being able to download OS patches or for deploying an SSH jumpbox/bastion) through tactical deployment of an IGW. Fully private cloud environments can be attained by presenting the VPC to AWS DirectConnect or creating a VPN between you internal (corporate) data centre and the VPC. Note, only one IGW can be assigned to a VPC (you cannot increase Internet bandwidth by attaching more!). Conversely, an IGW can be attached to just one VPC only.

Note, to access shared AWS services (such as, lambda, S3 & DynamoDB), the resource from which you are wanting access must be presented to the Internet - OR, depending on the shared service you can use VPC Peering. Word of caution, VPC Peering will consume some of the IP addresses in your VPC and significantly for lambda, their start up time is significantly impacted owing the lambda instance being dynamically assigned an IP address from your VPC.

With a VPC, you have one or more subnets. A default subnet is created within the default VPC for each availability zone within the zone, with a default route to the IGW (all subnets are public). A subnet cannot span more than one availability zone. Note, AWS reserves two IPs within each subnet, along with the typical 3 already reserved ([AWS Docs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)):
* x.x.x.0 - the network address
* x.x.x.1 - the default gateway
* x.x.x.2 - reserved (typically DNS)
* x.x.x.3 - reserved (for future use)
* x.x..x.N - broadcast

By default, traffic can only pass between subnets as defined by the default routing table (created with the VPC). When creating a subnet, by default, it will inherit the default route. It is good practice to NOT associate any IGW with the default route, and good practice to create separate routing tables for each subnet and define a route to the IGW individually - thus retaining control.

It is typical within a single VPC to have both public and private subnets:
* A public subnet is presented to the VPC's IGW (a default route).
* A private subnet is not presented to the VPC's IGW (it has no default route).

You would typically deploy your web servers to the public subnets and application/database servers to your private subnet.

Within a VPC you have available multiple layers of "network" security:
* Network ACL - allow/deny in priority order
* Security Groups - allow only and all must apply


![VPC Topology](/uploads/aws/aws-vpc-topology.png "AWS VPC Topology")

`Virtual Private Gateway`above is your VPN.


Steps to Create a VPC:
* Create the VPC - within a specific zone; give it an IP address range and a name. 
* Create one of more subnets within the VPC, each with their own IP range and in a given availability zone.
* Create separate route table for each subnets, and remember to assoicate it to the relevant subnet (beause be default all subnets get associated to the VPC's default route).
* Create an IGW, and associate it to the VPC.
* For public subnets, update the routing table adding a default route to IGW: `0.0.0.0/0` (IPv4) and `::/0` (IPv6)
* For public subnets, update network configuration to 'auto-assign public IPs' (disabled by default).
* Create necessary security groups to define ingres rules; note, you'll need at least one security group to allow SSH inbound (regardless of origin). This is true even if SSH'ing from a public EC2 instance/bastion to a private instance (both EC2 instances will need to allow SSH inbound).
	* __Note, it is good practice to have specific security group to control SSH ingres on private subnets, where the source CIDR (security group scope) is that of the public subnet.__

# NAT Gateway
NAT instance to be phased out; they are deployed into a given subnet/availabiity zone - does not scale easily (*instance type/AV zone/Bandwidth*). But a `NAT Instance` you deploy into a public subnet, which is then used by private subnets to provide tactical Internet outbound access (the private subnet is NAT'd on the outbound to the Internet thus retaining its private status).

A NAT Gateway is the preferred method having instant availability/scalability. Like a NAT instance, a NAT Gateway is provisioned into a pubnet subnet (so an AV), which can then be used by any private subnet within that AV. A NAT Gateway when provisioning requires an Elastic IP (from your public subnet). It takes about 10 to 15 minutes for AWS to provision a NAT Gateway. NAT Gateways are a cluster (providing availability) not requiring any patching/anti-virus/maintenance or failover configuration, supporting upto 10Gbit Internet bandwidth and do not need to be associated with a security group. NAT Gateways cannot be accessed and therefore are more secure than a NAT instance (which is only as secure as your configuration of it!).

**Then remember to update the routing table associated to the private subnet to have a default route 0.0.0.0/0 to the NAT gateway.**

It is noted, that a NAT Instance can be reused as a jumpbox/bastion.

# Bastion (jumpbox)
A secure approach to access EC2 instances in private subnets. An [AWS reference deployment](https://aws.amazon.com/quickstart/architecture/linux-bastion/) is available.

# Network ACL
A Network ACL (NACL) sits between a VPC's router and each of the its subnets. 

A subnet can be associated with a single NACL; but the same NACL can be used across multipl subnets (for example, the same NACL used across all public web subnets in each AV zone within the VPC).

A default NACL is created for each VPC and by default allow all traffic inbound and all traffic outbound. A NACL is the only VPC network service that can specifically block. A NACL is the only VPC network service that can control outbound flow. Conversely, when creating a new NACL, by default all traffic is block (inbound and outbound).

When creating a subnet, by default, it is assoicated to the default NACL.

When editting NACL rules, it is recommended to use ID increments of 100, making it easy to insert new rules.

NACL rules are stateless, which means you must explicitly define outbound rules; so where a service has ephemeral ports, those ports must be defined on the outbound set of NACL rules.

NACL rules are evaluated in sequence; as soon as a rule is matched to allow or deny, then that is applied to the traffic.

*You must remember to associate a NACL to a subnet to utilise the NACL rules.*

# Security Group
A security group is a network service that explicitly allows inbound traffic to a given destination; think of it as a filter into a given subnet. This compares to a NACL which is a network filter to **and** from the router

Unlike NACL (above), a security group only allows (can't block) and only covers inbound traffic (no outbound). A subnet can be associated to one or more security groups, making it easy to build complex relationships by adding (composing) security groups.

Unlike NACLs, all assigned security groups are evaulated before allowing traffic. Unlike NACLs, security groups are stateful (automatically allows outbound traffic associated with already allowed inbound traffic).

# Flow Logs
Network logging. First, you must capture the traffic at one of the three levels:
* VPC
* Subnet
* NIC

Logging is to a `CloudWatch Log Group`. Must create the `Log Group` before creating the flow log.

You can stream a CloudWatch Log Group to lamba/elasticsearch for realtime analysis.

What is not logged:
* Traffic to the subnet DNS (x.x.x.[2])
* Windows License Activation
* Traffic to/from 169.254.169.254 (instance metadata) - essentially this is internal to the EC2 instance not network traffic
* DHCP traffic
* Any traffic to the reserved IP address for the default VPC router