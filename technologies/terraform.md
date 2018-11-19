![Terraform Logo](/uploads/logos/terraform-logo.png "Terraform Logo"){.pagelogo}
<!-- TITLE: Terraform -->
<!-- SUBTITLE: A quick summary of Terraform -->

# Overview
Hashicorp's  [Terraform](https://www.terraform.io/) is Infrastructure As Code; able to provision resources across a wide variety of infrastructure including, but not limited to, private (VMware, OpenStack, KVM/libvirt) & public clouds (AWS, GCP, Azure), servers, network switches and firewalls, SANs, local & wide area networks and application platforms (Heruko).

In this respect, it is akin to AWS's CloudFormation and Hashicorp's other product, [vagrant](https://www.vagrantup.com/).

In this respect, it is nothing like puppet, chef and [ansible](/technologies/ansible); they are configuration management tools. Terraform can natively call upon chef having provided a server to consequently configure (provisioned) that server. Terraform can be made to play nice with ansible.

## Change Difference By Default
Like CloudFormation, Terraform takes a given configuration and compares it against the current state, producing a _change report_ that is (can) be reviewed before executing the changes.

However, it is therefore critical that if more than one person within a team, that the state as known by Terraform is shared; see below on [backend](#backend).

## Concurrent
Terraform uses both implicit (interpolation) and explicit dependencies between resources to sequence changes. The dependency is kept at resource level, within upon multiple resources within modules, to allow concurrency of execution of changes, thus scaling well to large infrastructures.




# Space

# More Space
fdshds
sgfds

dsfgh
fh



dfh

hfdh



fd

# Backend
