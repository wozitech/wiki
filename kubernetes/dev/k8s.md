---
title: My Dev k9s
description: 
published: true
date: 2020-02-02T14:20:09.030Z
tags: 
---

![Kubernetes Logo](/uploads/logos/kubernetes-logo.png "kubernetes Logo"){.pagelogo}
<!-- TITLE: My Dev k8s -->
<!-- SUBTITLE: My Development Kubernetes Cluster -->

# Topology
A two node cluster plus master, running on KVM provisioned via vagrant/ansible: https://github.com/wozitech/vagrant/tree/master/dev.

Deployed on a dev network only accessible within kvm environment.

k8s network stack is `weave`.

Proxied via untangle firewall (NAT/PAT).

* CoreDNS is running
* Dashboard - token login, and presented to local network on port 7433
* Kong Ingress Controller - presented on local network on port 6433
* Redis - installed via operator; one redis replica set presented using sentinel by cluster service "rfs-redisfailover"