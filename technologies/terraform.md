![Terraform Logo](/uploads/logos/terraform-logo.png "Terraform Logo"){.pagelogo}
<!-- TITLE: Terraform -->
<!-- SUBTITLE: A quick summary of Terraform -->

# Quick Reference
Tips & Tricks:
* https://blog.gruntwork.io/terraform-tips-tricks-loops-if-statements-and-gotchas-f739bbae55f9.
* https://itnext.io/things-i-wish-i-knew-about-terraform-before-jumping-into-it-43ee92a9dd65
* https://github.com/terraform-aws-modules/terraform-aws-vpc - Build an AWS VPC

```
terraform plan
terrafrom apply
terraform destroy
terraform init                                          <!-- when adding new modules -->
terraform taint -module my-vpc aws_instance.bastions.0  <!-- to mark a resource within a module as tainted - namely; to be rebuitl -->
```

WOZiTech public github repo: https://github.com/wozitech/terraform.
# Overview
Hashicorp's  [Terraform](https://www.terraform.io/) is Infrastructure As Code; able to provision resources across a wide variety of infrastructure including, but not limited to, private (VMware, OpenStack, KVM/libvirt) & public clouds (AWS, GCP, Azure), servers, network switches and firewalls, SANs, local & wide area networks and application platforms (Heruko).

In this respect, it is akin to AWS's CloudFormation and Hashicorp's other product, [vagrant](https://www.vagrantup.com).

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
Terraform supports the big public cloud providers: AWS (lambda but no step functions), Azure and GCP. I am using vagrant against a home KVM/libvirt server.

Terraform also supports [KVM/libvirt](https://github.com/dmacvicar/terraform-provider-libvirt) and [howto](https://computingforgeeks.com/how-to-provision-vms-on-kvm-with-terraform/).

# Provisioners
By default, Terraform supports local/remote commands, file (copies files/directory to remote resource) along with chef/habitat/salt. No direct support for ansible, but here's a guide on how to incorporate ansible: [https://alex.dzyoba.com/blog/terraform-ansible/](https://alex.dzyoba.com/blog/terraform-ansible/).

Note, when remotely provisioning AWS resources, the terraform host will need to connect to the remote instance; typically using SSH. This requires being able to SSH to the `ec2-user` (Amazon AMIs at least) having sudo privileges:
```
resource "aws_instance" "bastions" {
  ami           = "${data.aws_ami.bastion_ami.id}"
  instance_type = "t2.micro"
  vpc_security_group_ids = [
    "${aws_security_group.bastion-ssh-proxy.id}"
  ]
  key_name = "wozitech-1"
  depends_on = ["aws_internet_gateway.igw"]
  
  tags {
    Company = "wozitech"
  }


  connection {
    user = "ec2-user"
    private_key = "${file("${path.module}/priv.key")}"
  }

  provisioner "remote-exec" {
    inline = [
      "sudo groupadd -g 10000 aylingw"
    ]
  }
}
```

Above shows an AWS resource ([a bastion](https://github.com/wozitech/terraform/blob/master/modules/vpc/main.tf)), showing the security group allowing SSH inbound, the key pair `wozitech-1` and a `connection` definition which locates the private key for that key pair.

In the WOZ*iTech* reference AWS infrastucture, the bastion is the only host through which remote access is available. Need to consider how to provision with ansible and `connection` via a jumpbox. In addition, look at the following comment concerning `null_resource` on how to simulate a `vagrant provision` alternative with terraform taint: https://github.com/hashicorp/terraform/issues/3193#issuecomment-204771314.
# Modules
Terraform uses modules to decompose large complex infrastructures. 

Modules can be local (referenced by a relative path) or remote. 

Remote modules can in two forms:
* static repos like github/S3 - although these endpoints themselves support code maintenance, they do not support module versioning 
* versioned repos as per Terraform's [public repository] (https://registry.terraform.io) - akin to Ansible Galaxy/Docker Hub - or private 

Terraform Enterprise provides a local private repository. 

It is recommended to specify a module version to ensure expected behaviour. 

This article here describes how to use Nexus3 as a local private repo: https://dzone.com/articles/setting-up-a-docker-private-registry-with-authenti. 

Modules have input & output variables. Multiple instances of the same module can be created allowing for consistently duplicated target infrastructure. 

Dependencies can be created between resources across modules. The project top directory is the root module. 

[Recommended structure](https://www.terraform.io/docs/enterprise/workspaces/repo-structure.html) for a terraform project with local modules.
# Backend
Terraform's Backend is the focus of maintaining state (knowledge) of the target infrastructure. 

Out of the box, terraform default to local file. This is acceptable when there is just one developer (ignoring issues of availability) but does not allow for team support. 

Default support for teams is via Hashicorp's Consul. 

Multiple backends are supported, but of specific interest to me are:
* S3 - https://www.terraform.io/docs/backends/types/s3.html
* Nexus - https://medium.com/@arnobroekhof/using-sonatype-nexus-3-as-backend-provider-for-terraform-41e16d275fd7

# Enterprise
Terraform is an open source product. Hashicorp provide the main product to the community with no restrictions on its use. Hashicorp does also provide an Enterprise (paid for) edition. The enterprise edition includes:
* Shared Backend repository - as referred to in [backend](#backend) above
* Private Repository - as referred to in [modules](#modules) above
* Runtime environment - both an API for use by CI pipeline tools like Jenkins/AWS CodeDeploy and a queue, utilising a Version Control System (VCS) like github/gitlab