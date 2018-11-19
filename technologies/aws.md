![AWS Logo](/uploads/logos/aws-logo.png "AWS Logo"){.pagelogo}
<!-- TITLE: AWS -->
<!-- SUBTITLE: A quick summary of AWS -->

# Overview
AWS is a public cloud provder, with an extensive set of resources available on a pay per use (subscription) price model. A public cloud makes it easy to get into new technology, because you can start small and grow as load on your application/service grows.


## VPC
Virtual Private Cloud (VPC) unique describes the hosted provision of a given customer environment; it's a virtual data centre. Each customer account can have upto five VPC (default; can be increased) and every customer's VPC is unique to that customer, thus providing the separation of each customer's environment.

All VPC use private IP address space:
* 10.0.0.0/8 IP addresses: 10.0.0.0 -- 10.255.255.255
* 172.16.0.0/12 IP addresses: 172.16.0.0 -- 172.31.255.255
* 192.168.0.0/16 IP addresses: 192.168.0.0 – 192.168.255.255

Every account has one VPC by default; DO NOT DELETE THIS DEFAULT VPC.

A VPC can exist in a single region only.

A VPC allows each customer to perform detailed network configuration, restricting and presenting services/resources.

A VPC is typically presented to the Internet by attaching an Internet Gateway (IGW). But it does not have to be, allowing you to create private cloud environments. You can control which aspects of your private cloud environment do have Internet access (for example, being able to download OS patches or for deploying an SSH jumpbox) through tactical deployment of an IGW. Fully private cloud environments can be attained by presenting the VPC to AWS DirectConnect or creating a VPN between you internal (corporate) data centre and the VPC.

Note, to access shared AWS services (such as, lambda, S3 & DynamoDB), the resource from which you are wanting access must be presented to the Internet - OR, depending on the shared service you can use VPC Peering. Word of caution, VPC Peering will consume some of the IP addresses in your VPC and significantly for lambda, their start up time is significantly impacted owing the lambda instance being dynamically assigned an IP address from your VPC.

With a VPC, you have one or more subnets. A default subnet is created within the default VPC for each availability zone within the zone. A subnet cannot span more than one availability zone.

It is typical within a single VPC to have both public and private subnets:
* A public subnet is presented to the VPC's IGW (a default route).
* A private subnet is not presented to the VPC's IGW (it has no default route).

You would typically deploy your web servers to the public subnets and application/database servers to your private subnet.

Within a VPC you have available muplie layers of "network" security:
* Network ACL - allow/deny in priority order
* Security Groups - allow only - all apply


[VPC Topology](/uploads/aws/aws-vpc-topology.png "AWS VPC Topology")