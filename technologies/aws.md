![AWS Logo](/uploads/logos/aws-logo.png "AWS Logo"){.pagelogo}
<!-- TITLE: AWS -->
<!-- SUBTITLE: A quick summary of AWS -->

# Overview
AWS is a public cloud provder, with an extensive set of resources available on a pay per use (subscription) price model. A public cloud makes it easy to get into new technology, because you can start small and grow as load on your application/service grows.


## VPC
Virtual Private Cloud (VPC) unique describes the hosted provision of a given customer environment. Each customer account can have upto five VPC (default; can be increased) and every customer's VPC is unique to that customer, thus providing the separation of each customer's environment.

All VPC use private IP address space:
* 10.0.0.0/8 IP addresses: 10.0.0.0 -- 10.255.255.255
* 172.16.0.0/12 IP addresses: 172.16.0.0 -- 172.31.255.255
* 192.168.0.0/16 IP addresses: 192.168.0.0 – 192.168.255.255

Every account has one VPC by default; DO NOT DELETE THIS DEFAULT VPC.

A VPC is typically presented to the Internet by attaching an Internet Gateway (IGW). But it does not have to be, allowing you to create private cloud environments. You can control which aspects of your private cloud environment do have Internet access (for example, being able to download OS patches or for deploying an SSH jumpbox) through tactical deployment of an IGW. Fully private cloud environments can be attained by presenting the VPC to AWS DirectConnect.
