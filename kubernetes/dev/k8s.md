---
title: My Dev k9s
description: 
published: true
date: 2020-02-09T10:23:13.011Z
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
  * Deployed with `kong-proxy` service of type "loadbalancer" - bound to x.x.x.201 IP address on the master host
  * Deployed [without a database](https://github.com/Kong/kubernetes-ingress-controller/blob/master/docs/concepts/deployment.md) - a single pod with both "controller" and "data plane".
  Useful guides: https://github.com/Kong/kubernetes-ingress-controller/tree/master/docs/guides
  Kong Gateway OSS (open source - not enterprise)
* Redis - installed via operator; one redis replica set presented using sentinel by cluster service "rfs-redisfailover"


> Todo:
>  * Kong Operator - https://github.com/Kong/kong-operator
>  * kafka Operator - https://github.com/banzaicloud/kafka-operator
>  * kubeless - https://kubeless.io/

![wozitech_home_infrastructre_-_k8s_dev.png](/uploads/kubernetes/wozitech_home_infrastructre_-_k8s_dev.png)