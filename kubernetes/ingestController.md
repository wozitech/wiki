---
title: Kubernetes Ingest Controller
description: k8s ingest
published: true
date: 2020-02-02T10:43:56.434Z
tags: kubernetes, ingest, microservices
---

![Kubernetes Logo](/uploads/logos/kubernetes-logo.png "kubernetes Logo"){.pagelogo}

<!-- TITLE: k8s Ingress Controlller -->
<!-- SUBTITLE: A single http(s) entry point into k8s cluster -->

# References
* https://kubernetes.io/docs/concepts/services-networking/ingress/

#  Kong
An extension to nginx, Kong is an API gateway: https://github.com/Kong/kubernetes-ingress-controller.

Simple to install. As a ingress controller, Kong configuration (routes) can be updated from deployments into the cluster. No need for separate management.

Moreso, CRDs allows full control of Kong.