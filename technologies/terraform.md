![Terraform Logo](/uploads/logos/terraform-logo.png "Terraform Logo"){.pagelogo}
<!-- TITLE: Terraform -->
<!-- SUBTITLE: A quick summary of Terraform -->

# Overview
Hashicorp's  [Terraform](https://www.terraform.io/) is Infrastructure As Code; able to provision resources across a wide variety of infrastructure including, but not limited to, private (VMware, OpenStack, KVM/libvirt) & public clouds (AWS, GCP, Azure), servers, network switches and firewalls, SANs, local & wide area networks and application platforms (Heruko).

In this respect, it is akin to AWS's CloudFormation and Hashicorp's other product, [vagrant](https://www.vagrantup.com/ target="_blank").

In this respect, it is nothing like puppet, chef and [ansible](/technologies/ansible); they are configuration management tools. Terraform can natively call upon chef having provided a server to consequently configure (provisioned) that server. Terraform can be made to play nice with ansible.

## Change Difference By Default
Like CloudFormation, Terraform takes a given configuration and compares it against the current state, producing a _change plan_ that is (can) be reviewed before executing the changes. This is true of CloudFormation - it generates a set of change actions, but CloudFormation does not expose that change report and does not require it to be reviewed before executing. This control over, _change plan_ is a big win for Terraform.

However, it is therefore critical that if more than one person within a team, that the state as known by Terraform is shared; see below on [backend](#backend).

## Concurrent
Terraform uses both implicit (interpolation) and explicit dependencies between resources to sequence changes. The dependency is kept at resource level, within upon multiple resources within modules, to allow concurrency of execution of changes, thus scaling well to large infrastructures.

## Managed Existing Resources
CloudFormation (Stack) contain all resources that can be managed. You cannot add an existing AWS resource (like an EC2 instance) to be managed by CloudFormation. Terraform on the other hand does support managing existing resources, allowing you to introduce Terraform to an existing infrastructure - starting small with attention to the most critical aspects, and subsequently growing across the enterprise over time.

## No Wait Condition
CloudFormation has an in-built ability to wait on resources - for example, wait on an EC2 instance to complete (including post-config execution). Terraform does not have such a feature, but it's explicit dependencies can be used to simulate such wait characteristics.

## Rolling/Canary Updates
CloudFormation supports canary style/rolling updates across auto-scaling infrastructure; this is not currently supported by Terraform.

# Providers

# Provisioners

# Modules

# Backend

# Enterprise
Terraform is an open source product. Hashicorp provide the main product to the community with no restrictions on its use. Hashicorp does also provide an Enterprise (paid for) edition. The enterprise edition includes:
* Shared Backend repository - as referred to in [backend](#backend) above
* Private Repository - as referred to in [modules](#modules) above
* Runtime environment - both an API for use by CI pipeline tools like Jenkins/AWS CodeDeploy and a queue, utilising a Version Control System (VCS) like github/gitlab