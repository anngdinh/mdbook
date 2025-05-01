# GKE CNI insights

## GKE Dataplane V2

[About Dataplane V2](https://cloud.google.com/kubernetes-engine/docs/concepts/dataplane-v2)

- A consistent user experience for networking.
  - is built in to GKE on AWS, Azure, Google Distributed Cloud bare metal software (better than V1 because of this)
- Real-time visibility of network activity.
- Simpler architecture that makes it easier to manage and troubleshoot clusters.
- GKE use Calico (legacy) and Cilium (V2) to manage Kubernetes NetworkPolicy
  - Cilium uses eBPF
  - Calico Container Network Interface (CNI) uses `iptables` in the Linux kernel.
- Advantages
  - Scalability
    - does not use `kube-proxy`
    - does not rely on `iptables`
    - relies on `eBPF` maps that are limited to 64,000 endpoints across all services.
  - Security
    - NetworkPolicy is always on, don't have to install and manage third-party
  - Operations
    - network policy logging is built-in, can see when connections are allowed and denied by your Pods.
  - Consistency
    - it is the same across all GKE environments.
- technical specifications
- Limitations
  - can only be enabled when creating a new cluster
  - In certain cases, GKE Dataplane V2 agent Pods (anetd) can consume a significant amount of CPU resources, up to two or three vCPUs per instance. This occurs when there's a high volume of TCP connections being opened and closed rapidly on the node. To mitigate this problem, we recommend implementing keep-alives for HTTP calls and connection pooling for the relevant workloads.
  - ... [more](https://cloud.google.com/kubernetes-engine/docs/concepts/dataplane-v2#limitations)
- GKE Dataplane V2 does not use kube-proxy
