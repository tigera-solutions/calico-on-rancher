# Getting up and running with Calico on Rancher 

## Agenda

- Kubernetes networking considerations
  - Choose a pod cidr
  - Choose a service cidr
- Calico networking considerations
  - Choose which dataplane to use
  - Choose the initial IP Pool cidr
  - Choose the initial IP Pool block size
  - Choose the initial IP Pool nat outgoing mode
  - How to determine if encapsulation is required
- Install Rancher with Calico 
- Install Rancher with Calico Enterprise


### Kubernetes networking configuration

- Cluster pod cidr:               10.10.0.0/16
- Cluster service cidr:           10.20.0.0/16


### Calico networking configuration

- Calico dataplane:                  Standard Linux networking
- Calico initial/default IP Pool cidr:       10.10.0.0/16
- Calico initial IP Pool block size: /26 (default)
- Calico initial IP Pool nat mode:   enabled (default)
- Calico encapsulation mode: IPIP (CrossSubnet)

## Install Rancher with Calico(OSS)

### Requirements:

- kubectl/calicoctl installed ([here](https://kubernetes.io/docs/tasks/tools/install-kubectl/) and [here](https://docs.projectcalico.org/getting-started/clis/calicoctl/))
- [Rancher Server](https://rancher.com/docs/rancher/v2.x/en/installation/) with access to provision clusters
- Security Group/Firewall Allowing Communiction between worker nodes/control nodes based on [link](https://docs.projectcalico.org/getting-started/kubernetes/requirements)


### Sample Rancher Cluster Config

```
#
# Cluster Config
#
docker_root_dir: /var/lib/docker
enable_cluster_alerting: false
enable_cluster_monitoring: false
enable_network_policy: false
local_cluster_auth_endpoint:
  enabled: true
name: nico-calico-oss
#
# Rancher Config
#
rancher_kubernetes_engine_config:
  addon_job_timeout: 30
  authentication:
    strategy: x509
  dns:
    nodelocal:
      ip_address: ''
      node_selector: null
      update_strategy: {}
  ignore_docker_version: true
#
# # Currently only nginx ingress provider is supported.
# # To disable ingress controller, set `provider: none`
# # To enable ingress on specific nodes, use the node_selector, eg:
#    provider: nginx
#    node_selector:
#      app: ingress
#
  ingress:
    provider: nginx
  kubernetes_version: v1.17.5-rancher1-1
  monitoring:
    provider: metrics-server
    replicas: 1
#
#   If you are using calico on AWS
#
#    network:
#      plugin: calico
#      calico_network_provider:
#        cloud_provider: aws
#
# # To specify flannel interface
#
#    network:
#      plugin: flannel
#      flannel_network_provider:
#      iface: eth1
#
# # To specify flannel interface for canal plugin
#
#    network:
#      plugin: canal
#      canal_network_provider:
#        iface: eth1
#
  network:
    plugin: calico
#
#    services:
#      kube-api:
#        service_cluster_ip_range: 10.43.0.0/16
#      kube-controller:
#        cluster_cidr: 10.42.0.0/16
#        service_cluster_ip_range: 10.43.0.0/16
#      kubelet:
#        cluster_domain: cluster.local
#        cluster_dns_server: 10.43.0.10
#
  services:
    etcd:
      backup_config:
        enabled: true
        interval_hours: 12
        retention: 6
        safe_timestamp: false
      creation: 12h
      extra_args:
        election-timeout: 5000
        heartbeat-interval: 500
      gid: 0
      retention: 72h
      snapshot: false
      uid: 0
    kube_api:
      always_pull_images: false
      pod_security_policy: false
      service_node_port_range: 30000-32767
      service_cluster_ip_range: 10.20.0.0/16
    kube-controller:
        cluster_cidr: 10.10.0.0/16
        service_cluster_ip_range: 10.20.0.0/16
    kubelet:
        cluster_domain: cluster.local
        cluster_dns_server: 10.20.0.10
  ssh_agent_auth: false
  upgrade_strategy:
    drain: false
    max_unavailable_controlplane: '1'
    max_unavailable_worker: 10%
    node_drain_input:
      delete_local_data: 'false'
      force: false
      grace_period: -1
      ignore_daemon_sets: true
      timeout: 120
windows_prefered_cluster: false
```

## Install Rancher with Calico Enterprise

### Sample Rancher Cluster Config


```
#
# Cluster Config
#
docker_root_dir: /var/lib/docker
enable_cluster_alerting: false
enable_cluster_monitoring: false
enable_network_policy: false
local_cluster_auth_endpoint:
  enabled: true
name: nico-calico-oss
#
# Rancher Config
#
rancher_kubernetes_engine_config:
  addon_job_timeout: 30
  authentication:
    strategy: x509
  dns:
    nodelocal:
      ip_address: ''
      node_selector: null
      update_strategy: {}
  ignore_docker_version: true
#
# # Currently only nginx ingress provider is supported.
# # To disable ingress controller, set `provider: none`
# # To enable ingress on specific nodes, use the node_selector, eg:
#    provider: nginx
#    node_selector:
#      app: ingress
#
  ingress:
    provider: nginx
  kubernetes_version: v1.17.5-rancher1-1
  monitoring:
    provider: metrics-server
    replicas: 1
#
#   If you are using calico on AWS
#
#    network:
#      plugin: calico
#      calico_network_provider:
#        cloud_provider: aws
#
# # To specify flannel interface
#
#    network:
#      plugin: flannel
#      flannel_network_provider:
#      iface: eth1
#
# # To specify flannel interface for canal plugin
#
#    network:
#      plugin: canal
#      canal_network_provider:
#        iface: eth1
#
  network:
    plugin: none
#
#    services:
#      kube-api:
#        service_cluster_ip_range: 10.43.0.0/16
#      kube-controller:
#        cluster_cidr: 10.42.0.0/16
#        service_cluster_ip_range: 10.43.0.0/16
#      kubelet:
#        cluster_domain: cluster.local
#        cluster_dns_server: 10.43.0.10
#
  services:
    etcd:
      backup_config:
        enabled: true
        interval_hours: 12
        retention: 6
        safe_timestamp: false
      creation: 12h
      extra_args:
        election-timeout: 5000
        heartbeat-interval: 500
      gid: 0
      retention: 72h
      snapshot: false
      uid: 0
    kube_api:
      always_pull_images: false
      pod_security_policy: false
      service_node_port_range: 30000-32767
      service_cluster_ip_range: 10.20.0.0/16
    kube-controller:
        cluster_cidr: 10.10.0.0/16
        service_cluster_ip_range: 10.20.0.0/16
    kubelet:
        cluster_domain: cluster.local
        cluster_dns_server: 10.20.0.10
  ssh_agent_auth: false
  upgrade_strategy:
    drain: false
    max_unavailable_controlplane: '1'
    max_unavailable_worker: 10%
    node_drain_input:
      delete_local_data: 'false'
      force: false
      grace_period: -1
      ignore_daemon_sets: true
      timeout: 120
windows_prefered_cluster: false

```

### Requirements:

- Standard Calico  Enterprise [Requirements](https://docs.tigera.io/getting-started/kubernetes/self-managed-on-prem/rancher#create-a-compatible-rancher-cluster)
- kubectl/calicoctl installed ([here](https://kubernetes.io/docs/tasks/tools/install-kubectl/) and [here](https://docs.projectcalico.org/getting-started/clis/calicoctl/))
- [Tigera Credential / License](https://docs.tigera.io/getting-started/calico-enterprise)
- Rancher Server with access to provision clusters
- Ability to provision PV from a StorageClass


## References

* [Calico Enterprise Installation on Rancher](https://docs.tigera.io/getting-started/kubernetes/self-managed-on-prem/rancher#create-a-compatible-rancher-cluster)
* [Calico Enterprise Requirements](https://docs.tigera.io/getting-started/kubernetes/requirements)
* [Calico IPAM Configuration](https://docs.projectcalico.org/networking/ipam)