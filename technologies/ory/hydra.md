---
title: Ory Hydra
description: 
published: true
date: 2020-03-01T12:06:50.155Z
tags: Hydra, Authentication Server, OAuth2, OpenID
---

# References
* https://github.com/ory/hydra - the source
* https://github.com/segmentio/ory-hydra/blob/master/docs/oauth2.md - Hydra docs
& https://www.ory.sh/docs/hydra/ - the official docs
* https://hub.docker.com/r/oryd/hydra/ - DockerHub image
* https://www.ory.sh/run-oauth2-server-open-source-api-security/ - an easy example for setting up Hydra

# Notes
Hydra itself is not an identify provider; it orchestrates the process to allow other clients to use your identify provider - namely, LDAP or Active Directory (locally hosted) or AWS Cognito (cloud).