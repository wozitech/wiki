---
title: My Dev k9s
description: 
published: true
date: 2020-07-19T10:45:22.051Z
tags: 
---

![Kubernetes Logo](/uploads/logos/kubernetes-logo.png "kubernetes Logo"){.pagelogo}
<!-- TITLE: My Dev k8s -->
<!-- SUBTITLE: My Development Kubernetes Cluster -->

# Topology
A two node cluster plus master, running on KVM provisioned via vagrant/ansible: https://github.com/wozitech/vagrant/tree/master/dev.

The master API is not exposed. All deployments are done via logging on to the master, and becoming user `kong`.

The master has `kustomize` and `helm` installed: `/usr/local/bin`.

Deployed on a dev network only accessible within kvm environment.

k8s network stack is `weave`.

Proxied via untangle firewall (NAT/PAT).

* CoreDNS is running
* Dashboard - token login, and presented to local network on port 7433
* Kong Ingress Controller - presented on local network on port 6433
  * Deployed with `kong-proxy` service of type "loadbalancer" - bound to x.x.x.201 IP address on the master host
  * Deployed [without a database](https://github.com/Kong/kubernetes-ingress-controller/blob/master/docs/concepts/deployment.md) - a single pod with both "controller" and "data plane".
  * Useful guides: https://github.com/Kong/kubernetes-ingress-controller/tree/master/docs/guides
  * Kong Gateway OSS (open source - not enterprise)
  
```
  ---
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-backend-protocol: tcp
    service.beta.kubernetes.io/aws-load-balancer-type: nlb
  name: kong-proxy
  namespace: kong
spec:
  externalTrafficPolicy: Local
  ports:
  - name: proxy
    port: 80
    protocol: TCP
    targetPort: 8000
  - name: proxy-ssl
    port: 443
    protocol: TCP
    targetPort: 8443
  selector:
    app: ingress-kong
  type: LoadBalancer
  externalIPs:
    - 192.168.100.201
```
* Redis - installed via operator; one redis replica set presented using sentinel by cluster service "rfs-redisfailover"


## kakfa (Confluence) operator
kafka and zookeeper require persistent storage. As a baremetal k8s cluster, I needed to add a default storage class and present persistent volumes on each node:

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage-class
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: Immediate
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-kafka-persistedvolume-node1
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: local-storage-class
  local:
     path: /var/lib/k8s-storage
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - k8s-worker1.dev.wozitech-ltd.local
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-kafka-persistedvolume-node2
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: local-storage-class
  local:
     path: /var/lib/k8s-storage
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - k8s-worker2.dev.wozitech-ltd.local
```

As part of the operator installation prep, I copied `.../helm/provider/private.yaml` to create `.../helm/provider/wozitech.yaml`:

```
## Overriding values for Chart's values.yaml
## Example values to run Confluent Operator in Private Cloud
global:
  provider:
    name: private
    ## if any name which indicates regions
    #### Overriding values for Chart's values.yaml
## Example values to run Confluent Operator in Private Cloud
global:
  provider:
    name: private
    ## if any name which indicates regions
    ##
    region: anyregion
    kubernetes:
       deployment:
         ## If kubernetes is deployed in multi zone mode then specify availability-zones as appropriate
         ## If kubernetes is deployed in single availability zone then specify appropriate values
         ## For the private cloud, use kubernetes node labels as appropriate
         zones:
          - myzones
    ##
    ## Docker registry endpoint where Confluent Images are available.
    ##
    registry:
      fqdn: docker.io
      credential:
        required: false
  sasl:
    plain:
      username: test
      password: test123
  authorization:
    rbac:
      enabled: false
    simple:
      enabled: false
    superUsers: []
  dependencies:
    mds:
      endpoint: ""
      publicKey: ""

## operator
operator:
  namedspaced: true

## Zookeeper cluster
##
zookeeper:
  name: zookeeper
  replicas: 3
  resources:
    requests:
      cpu: 200m
      memory: 512Mi

## Kafka Cluster
##
kafka:
  name: kafka
  replicas: 3
  resources:
    requests:
      cpu: 200m
      memory: 1Gi
  loadBalancer:
    enabled: false
    domain: ""
  tls:
    enabled: false
    fullchain: |-
    privkey: |-
    cacerts: |-
  metricReporter:
    enabled: false

## Connect Cluster
##
##connect:
##  name: connectors
##  replicas: 2
##  tls:
##    enabled: false
##    ## "" for none, "tls" for mutual auth
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      bootstrapEndpoint: kafka:9071
##      brokerCount: 3
##    schemaRegistry:
##      enabled: true
##      url: http://schemaregistry:8081
## Replicator Connect Cluster
##
##replicator:
##  name: replicator
##  replicas: 2
##  tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071
##
## Schema Registry
##
##schemaregistry:
##  name: schemaregistry
## tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071

##
## KSQL
##
##ksql:
##  name: ksql
##  replicas: 2
##  tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071
##      brokerEndpoints: kafka-0.kafka:9071,kafka-1.kafka:9071,kafka-2.kafka:9071
##    schemaRegistry:
##      enabled: false
##      tls:
##        enabled: false
##        authentication:
##          type: ""
##      url: http://schemaregistry:8081

## Control Center (C3) Resource configuration
##
##controlcenter:
##  name: controlcenter
##  license: ""
##  ##
##  ## C3 dependencies
##  ##
##  dependencies:
##    c3KafkaCluster:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071
##      zookeeper:
##        endpoint: zookeeper:2181
##    connectCluster:
##      enabled: true
##      url: http://connectors:8083
##    ksql:
##      enabled: true
##      url: http://ksql:9088
##    schemaRegistry:
##      enabled: true
##      url: http://schemaregistry:8081
##  ##
##  ## C3 External Access
##  ##
##  loadBalancer:
##    enabled: false
##    domain: ""
##  ##
##  ## TLS configuration
##  ##
##  tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  ##
##  ## C3 authentication
##  ##
##  auth:
##    basic:
##      enabled: true
##      ##
##      ## map with key as user and value as password and role
##      property:
##        admin: Developer1,Administrators
##        disallowed: no_access

    region: anyregion
    kubernetes:
       deployment:
         ## If kubernetes is deployed in multi zone mode then specify availability-zones as appropriate
         ## If kubernetes is deployed in single availability zone then specify appropriate values
         ## For the private cloud, use kubernetes node labels as appropriate
         zones:
          - myzones
    ##
    ## Docker registry endpoint where Confluent Images are available.
    ##
    registry:
      fqdn: docker.io
      credential:
        required: false
  sasl:
    plain:
      username: test
      password: test123
  authorization:
    rbac:
      enabled: false
    simple:
      enabled: false
    superUsers: []
  dependencies:
    mds:
      endpoint: ""
      publicKey: ""
  storageClassName: local-storage-class

## operator
operator:
  namedspaced: true

## Zookeeper cluster
##
zookeeper:
  name: zookeeper
  replicas: 3
  resources:
    requests:
      cpu: 200m
      memory: 512Mi

## Kafka Cluster
##
kafka:
  name: kafka
  replicas: 3
  resources:
    requests:
      cpu: 200m
      memory: 1Gi
  loadBalancer:
    enabled: false
    domain: ""
  tls:
    enabled: false
    fullchain: |-
    privkey: |-
    cacerts: |-
  metricReporter:
    enabled: false

## Connect Cluster
##
##connect:
##  name: connectors
##  replicas: 2
##  tls:
##    enabled: false
##    ## "" for none, "tls" for mutual auth
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      bootstrapEndpoint: kafka:9071
##      brokerCount: 3
##    schemaRegistry:
##      enabled: true
##      url: http://schemaregistry:8081
## Replicator Connect Cluster
##
##replicator:
##  name: replicator
##  replicas: 2
##  tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071
##
## Schema Registry
##
##schemaregistry:
##  name: schemaregistry
## tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071

##
## KSQL
##
##ksql:
##  name: ksql
##  replicas: 2
##  tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  loadBalancer:
##    enabled: false
##    domain: ""
##  dependencies:
##    kafka:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071
##      brokerEndpoints: kafka-0.kafka:9071,kafka-1.kafka:9071,kafka-2.kafka:9071
##    schemaRegistry:
##      enabled: false
##      tls:
##        enabled: false
##        authentication:
##          type: ""
##      url: http://schemaregistry:8081

## Control Center (C3) Resource configuration
##
##controlcenter:
##  name: controlcenter
##  license: ""
##  ##
##  ## C3 dependencies
##  ##
##  dependencies:
##    c3KafkaCluster:
##      brokerCount: 3
##      bootstrapEndpoint: kafka:9071
##      zookeeper:
##        endpoint: zookeeper:2181
##    connectCluster:
##      enabled: true
##      url: http://connectors:8083
##    ksql:
##      enabled: true
##      url: http://ksql:9088
##    schemaRegistry:
##      enabled: true
##      url: http://schemaregistry:8081
##  ##
##  ## C3 External Access
##  ##
##  loadBalancer:
##    enabled: false
##    domain: ""
##  ##
##  ## TLS configuration
##  ##
##  tls:
##    enabled: false
##    authentication:
##      type: ""
##    fullchain: |-
##    privkey: |-
##    cacerts: |-
##  ##
##  ## C3 authentication
##  ##
##  auth:
##    basic:
##      enabled: true
##      ##
##      ## map with key as user and value as password and role
##      property:
##        admin: Developer1,Administrators
##        disallowed: no_access
```
**Note - **

The Confluence operator can be installed into a given namespace and the kafka cluster can be created in that same namespace.

```
export VALUES_FILE=.../operators/confluence/helm/providers/wozitech.yaml
helm upgrade --install   operator   ./confluent-operator   --values $VALUES_FILE   --namespace kafka   --set operator.enabled=true

helm upgrade --install \
  zookeeper \
  ./confluent-operator \
  --values $VALUES_FILE \
  --namespace kafka \
  --set zookeeper.enabled=true
```


---

> Todo:
>  * ~~Kong Operator - https://github.com/Kong/kong-operator~~
>  * kafka Operator - https://github.com/banzaicloud/kafka-operator
>  * kubeless - https://kubeless.io/
>  * ORK Kratos, Hyrda and Kets - Authentication and Authorisation

![wozitech_home_infrastructre_-_k8s_dev.png](/uploads/kubernetes/wozitech_home_infrastructre_-_k8s_dev.png)

