---
title: Kubernetes Ingest Controller
description: k8s ingest
published: true
date: 2020-02-02T11:41:10.278Z
tags: kubernetes, ingest, microservices
---

![Kubernetes Logo](/uploads/logos/kubernetes-logo.png "kubernetes Logo"){.pagelogo}

<!-- TITLE: k8s Ingress Controlller -->
<!-- SUBTITLE: A single http(s) entry point into k8s cluster -->

# References
* https://kubernetes.io/docs/concepts/services-networking/ingress/

# Introduction
An ingress controller is an http/https entry point into a k8s cluster. k8s supports more than one ingress controller - deployments name the specific controller they are using.

#  Kong
An extension to nginx, Kong is an API gateway: https://github.com/Kong/kubernetes-ingress-controller.

Simple to install.

It is not your typical ingress controller implementation, Kong extends the ingress controller API with CRD (Custom Resource Defintions) to allow for the support of proxy behaviour. 

Additional CRDs are created during install which allow for the full control (mgmt) of Kong.