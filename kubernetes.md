![Kubernetes Logo](/uploads/logos/kubernetes-logo.png "kubernetes Logo"){.pagelogo}
<!-- TITLE: kubernetes -->
<!-- SUBTITLE: The De Facto App Hosting Platform -->
# References
* https://www.mvps.net/docs/building-a-kubernetes-cluster-k8s-on-centos-7-in-a-kvm/ - howto build a KVM K8s (cluster)

# Background
Originally developed by Google, and donated to open source community.

Requires a `master` node and one of more `worker` nodes (PODs). K8s can be installed as a standalone host - both master and one worker; this is how Microsoft Docker Desktop works (a hyperV VM being the k8s, against which docker images are deployed and executed).

A Pod is an application specific "logical host"; e.g. web servers, app1 servers, ...

On the `master` is found:
* `API Server` - REST API using JSON/yaml
* `Scheduler` - scheduled tasks, such as, launching contrainers of worker nodes
* `Controller Manager` - monitors and scales the current number of pods 
* `etcd` - fast key-value store
* `kubectl` utility - command line access to API Server

On the `worker` is found:
* `kubelet` - daemon for creating, launching and deleting containers
* `kube-proxy` - routes network traffic to/from the container
* `pod` - application _logical set_ on this worker.


# Monitoring
## Prometheus
