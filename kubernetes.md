![Kubernetes Logo](/uploads/logos/kubernetes-logo.png "kubernetes Logo"){.pagelogo}
<!-- TITLE: kubernetes -->
<!-- SUBTITLE: The De Facto App Hosting Platform -->
# References
* https://www.mvps.net/docs/building-a-kubernetes-cluster-k8s-on-centos-7-in-a-kvm/ - howto build a KVM K8s (cluster)
* https://kubernetes.io/blog/2019/03/15/kubernetes-setup-using-ansible-and-vagrant/ - vagrant and ansible

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

# Installation
One master and two workers. vagrant/kvm/centros7/ansible/weave scripted deployment: https://github.com/wozitech/vagrant/tree/master/dev.

The `master` node needs a non-root user to run `kube*` commands via. The user/group `kube` is created for that purpose - consistently across all hosts, even though it is only used on the `master`. During installation on master, the worker join command is generated and copied to a local ansible file, which is then copied to the worker and executed as `root`. Once a worker node has joined the cluster, it can be administered from the `master`.

ks8 networking can be managed via one of many container network interface plugins, such as, weave or flannel. A comparison of the four major CNIs: https://rancher.com/blog/2019/2019-03-21-comparing-kubernetes-cni-providers-flannel-calico-canal-and-weave/.

To check cluster:
* `kubectl get nodes`
* `kubectl get pods --all-namespaces`

During development of ansible scripts, I did have to remove a node from the cluster; on the worker node, `kubeadm reset`.

### TODO
1. Add public ethernet - allowing pods to be presented to different networks
2. Mount vda2 disks for docker images
3. docker `cgroupfs` to `systemd`

_Note: the master node needs a  minimum of 2 (v)CPUs, and docker "cgroupfs" must be systemd not cgroup driver._
_Note: docker cgroupfs should be systemd not cgroups (as default on install)._

# Administration
## Drain
To perform maintenance on a k8s worker node, drain all apps (pods) first. On the master: `kubectl drain <name of worker>`.

# Monitoring
## Prometheus
