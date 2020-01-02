---
title: kubernetes
description: 
published: true
date: 2020-01-02T16:46:10.068Z
tags: 
---

![Kubernetes Logo](/uploads/logos/kubernetes-logo.png "kubernetes Logo"){.pagelogo}
<!-- TITLE: kubernetes -->
<!-- SUBTITLE: The De Facto App Hosting Platform -->
# References
* https://www.mvps.net/docs/building-a-kubernetes-cluster-k8s-on-centos-7-in-a-kvm/ - howto build a KVM K8s (cluster)
* https://kubernetes.io/blog/2019/03/15/kubernetes-setup-using-ansible-and-vagrant/ - vagrant and ansible

Look some more at:
* Kaniko- https://github.com/GoogleContainerTools/kaniko
* Istio - https://istio.io/docs/concepts/what-is-istio/
* OpenTracing - https://www.hawkular.org/blog/2017/06/26/opentracing-appmetrics.html

Looking at using kubernetes to deploy distributed application services over a service mesh with full service tracing.

# Background
Originally developed by Google, and donated to open source community.

Requires a `master` node and one of more worker `nodes` (formerly called minions). K8s can be installed as a standalone host - both master and one worker; this is how Microsoft Docker Desktop works (a hyperV VM being the k8s, against which docker images are deployed and executed).

A Pod is an application specific "logical host"; e.g. web servers, app1 servers, ...

On the `master` is found - assume `kube` user:
* `API Server` - REST API using JSON/yaml
* `Scheduler` - scheduled tasks, such as, launching contrainers of worker nodes
* `Controller Manager` - monitors and scales the current number of pods 
* `etcd` - fast key-value store
* `kubectl` utility - command line access to API Server


On the `node` is found - assume `kube` user:
* `kubelet` - daemon for creating, launching and deleting containers
* `kube-proxy` - routes network traffic to/from the container
* `pod` - application _logical set_ on this worker - consisting of one (atypical) or more containers that work as a single unit.

A `deployment` is a `replication set` of pods across the k8s; it defines, as a minimum, the number of pods required across the cluster. The `deployment` ensures this number of pods is maintained, regardless of whether a pod fails, the host on which a pod fails, of the number of pods as defined within the deployment is changed. A single node can have more than one instance of the same pod running on it (if deployment says 4 but only three nodes, then one node must run two pod instances). A `deployment` is managed; it manages the pods including supporting rolling updates. 

`kubectl get --all-namespaces deployments`

A `service` is the single entry point to a given deployment; no matter when/if the configuration of the deployment changes, the `service` identity does not change. A `service` has a common IP address, port and label selector making it available for external use. A `service` can be discovered by other `services` - for example, a `frontend` service can discovered and thus use a `backend` service.

By default, when a service is created, it is created with a Cluster IP address; that is a address known within the kubernetes cluster.

There are other `ServiceTypes` - taken https://kubernetes.io/docs/concepts/services-networking/service/:
* `NodePort`: Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting `<NodeIP>:<NodePort>`.
* `LoadBalancer`: Exposes the Service externally using a **cloud provider’s** load balancer.
  * `ExternalIP`: within the service definition yaml file can additionally specify the externalIPs of the load balancer.
  * If using `minikube` can run `minikube tunnel`, and still use `loadbalancer` type.
* `ExternalName`: Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record


A `service` can be discovered in one of two ways:
* Environment Variable - only works between services on the same node - and therefore, not scalable
* Cluster DNS (by DNS name) - preferre, and comprises of:
	* `etcd` - for distributing the DNS values across the clsuter for read and write
	* `skydns` - a DNS service that can read from `etcd`
	* `kube2sky` - watches for `service` changes on the master and write to `etcd` thus consequentially making the service available via DNS

A `Daemon Set` is replication set that runs on _all_ nodes within the cluster (such as, cluster storage systems, application caches, like Hasicorp consul and log collectors, like logstash. It would be typical to split a daemon based on cluster  node profile - for example, the size of node (RAM or local storage) - it wouldn't make sense to deploy a large storage cache to a node with limited storage. For this to work best, nodes should be properly labelled (described/tagged).

A `services` can interacrt with a `daemon` in one of four ways:
* push - the pods (on the node) are configured to push data to the daemon - the daemons are not discoverable
* IP and port - the pods use the node's (host) IP and port for the daemon
* DNS - the daemons are accessed by name
* Service - the daemons are presented as a service, and pods on the host coukld use a daemon on a different host, or most importantly, still access a daemon if the host on which the pod has been launched doesn't have such a daemon instance (e.g. a storage cluster daemon on a node with no local storage)

A `namespace` is a virutalised set of services & pods. It allows a single k8s cluster to be separated in many ways. e.g. a "dev" namespace, or a "systemtest" namespace. To `get` or `describe` or `expose` for a specific namesapce, use the `--namesace <my namespace>` argument to `kubectl`.

# Installation
One master and two workers. vagrant/kvm/centros7/ansible/weave scripted deployment: https://github.com/wozitech/vagrant/tree/master/dev.

The `master` node needs a non-root user to run `kube*` commands via. The user/group `kube` is created for that purpose - consistently across all hosts, even though it is only used on the `master`. During installation on master, the worker join command is generated and copied to a local ansible file, which is then copied to the worker and executed as `root`. Once a worker node has joined the cluster, it can be administered from the `master`.

ks8 networking can be managed via one of many container network interface plugins, such as, weave or flannel. A comparison of the four major CNIs: https://rancher.com/blog/2019/2019-03-21-comparing-kubernetes-cni-providers-flannel-calico-canal-and-weave/.

To check cluster:
* `kubectl get nodes`
* `kubectl get pods --all-namespaces -o wide`

During development of ansible scripts, I did have to remove a node from the cluster; on the worker node, `kubeadm reset`.

### Web UI
The [kubernetes web dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) is not installed (deployed) by default.

To install: `kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta4/aio/deploy/recommended.yaml`. This deploys the web UI as a pod (application). 

Need to create an `admin-user` and get it's token as described [here](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md). **Save each of the service account user and ClusterRoleBinding defs in separate files - and run `kubectl apply -f <name of file>.yaml` separately, or a single file using `---` to separate each.**

To access this dashboard outside of the k8s cluster, need to create a non-clutserIP service:
```
kubectl expose --namespace kubernetes-dashboard deployment kubernetes-dashboard --type=LoadBalancer --name=lb-kubernetes-dashboard

or

kubectl expose --namespace kubernetes-dashboard deployment kubernetes-dashboard --type=NodePort --name=port-kubernetes-dashboard
```
_Using a "LoadBalancer" service type, will automatically apply a NodePort binding too._ Having created the service, can then get to the host's port:
```
kubectl describe --namespace kubernetes-dashboard services
```

Can then port forward/forward proxy to the to any one of the k8s host (all will use the same port and resolve internally to whichever node the dashboard app is running on).


### TODO
1. Add public ethernet - allowing pods to be presented to different networks
2. Mount vda2 disks for docker images; PSI plugins for persistent storage stateful containers
3. docker `cgroupfs` to `systemd`

_Note: the master node needs a  minimum of 2 (v)CPUs, and docker "cgroupfs" must be systemd not cgroup driver._
_Note: docker cgroupfs should be systemd not cgroups (as default on install)._

# Administration
## Drain
To perform maintenance on a k8s worker node, drain all apps (pods) first. On the master: `kubectl drain <name of worker>`.

# Monitoring
## Prometheus

# DNS
With kubernetes DNS service installed, e.g. kube-dns or core-dns, then services and pods can be resolved by DNS name. Taken from https://kubernetes.io/docs/concepts/services-networking/service/,

If you have a Service called "my-service" in a Kubernetes Namespace "my-ns", the control plane and the DNS Service acting together create a DNS record for "my-service.my-ns".

Kubernetes also supports DNS SRV (Service) records for named ports. If the "my-service.my-ns" Service has a port named "http" with protocol set to TCP, you can do a DNS SRV query for _http._tcp.my-service.my-ns to discover the port number for "http", as well as the IP address.

To debug DNS is can be useful to run up the following interactive container: `kubectl run curl --image=radial/busyboxplus:curl -i --tty`.

Once in, issue `nslookup bob` and it will return the DNS domain server used and it's fully qualified domain name. e.g.:
```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local
```

In this example, `svc.cluster.local` is the cluster root DNS domain.

To lookup say the dashboard:
```
nslookup kubernetes-dashboard.kubernetes-dashboard.svc.cluster.local
```

Note - `kubernetes-dashboard` is both the name of the container and the namespace.

# CLI
For those having docker experience: https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/.

## Available k8s Services
`kubectl api-resources`

e.g. `pods`, `nodes`, `jobs`, `networkpolicies`, ...

## Services
* `kubectl get services`
* `kubectl get services --all-namespaces`
* `kubectl get services --namespace wd-audio-platform-local -o wide` - returns full details, including a column highlighting those that have External-IP and what that IP is

## Pods (Apps by node)
* `kubectl get pods --all-namespaces` - all pods
* `kubectl get pods --namespace <namespace>` - use `-o wide` to get more information
* `kubectl get pods --namespace <namespace> --sort-by='.status.containerStatuses[0].restartCount' - to sort by restarts
* `kubectl describe pod --namespace <namespace> <pod id>`

## Deployments
* `kubectl get --namespace <namespace> deployments.v1.apps`
* `kubectl get --all-namespaces deployments`
