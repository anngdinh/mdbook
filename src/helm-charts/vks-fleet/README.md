# VKS Fleet

## Multi-Cluster Services API [link](https://github.com/kubernetes/enhancements/tree/master/keps/sig-multicluster/1645-multi-cluster-services-api#release-signoff-checklist)

### Goals

- Consume a service in another cluster (or multiple clusters) as a single service.
- When a service is consumed from another cluster its behavior should be predictable and consistent with how it would be consumed within its own cluster.
- Allow gradual rollout of changes in a multi-cluster environment.
- Create building blocks for multi-cluster tooling.
- Support multiple implementations.
- Leave room for future extension and new use cases.

### Terminology

- **clusterset** - name for a group of clusters
  - namespace sameness
- **mcs-controller** - A controller that syncs services across clusters and makes them available for multi-cluster service discovery and connectivity.

### CRDs

- ServiceExport
  - specify which services should be exposed across all clusters in the clusterset
  - created in each cluster that the underlying Service resides in
- ServiceImport
  - managed by the MCS implementation's mcs-controller.
  - created in each cluster that wants to consume the service
  - contains the information needed to connect to all exported services

### User Stories

- Different ClusterIP Services Each Deployed to Separate Cluster
  - service frontend in cluster A and service backend in cluster B
  - service frontend wants to consume service backend via a single DNS name
  - if migrate service backend to cluster C, service frontend should not need to be updated
- Single Service Deployed to Multiple Clusters
  - have multiple stateless deploy backend services in different clusters in local, regional, global
  - fronend services in any clusters can access backend in priority order based on availability and locality.
  - Routing to service should optimize for cost metric (e.g. prioritize traffic local to zone, region).

### Design Details

```yaml
apiVersion: multicluster.k8s.io/v1alpha1
kind: ServiceExport
metadata:
  name: my-svc
  namespace: my-ns
status:
  conditions:
  - type: Ready
    status: "True"
    lastTransitionTime: "2020-03-30T01:33:51Z"
  - type: InvalidService
    status: "False"
    lastTransitionTime: "2020-03-30T01:33:55Z"
  - type: Conflict
    status: "True"
    lastTransitionTime: "2020-03-30T01:33:55Z"
    message: "Conflicting type. Using \"ClusterSetIP\" from oldest service export in \"cluster-1\". 2/5 clusters disagree."
```

- A `Service` without a corresponding `ServiceExport` in its local cluster will not be exported even if other clusters are exporting a `Service` with the same namespaced name.
- All information about the service, including ports, backends and topology, will continue to be stored in the Service objects, which are each name mapped to a ServiceExport.
- Deleting a ServiceExport will stop exporting the name-mapped Service
- Should use RBAC rules to prevent creation of `ServiceExports` in select namespaces (`default`, `kube-system`, etc.)

```yaml
apiVersion: multicluster.k8s.io/v1alpha1
kind: ServiceImport
metadata:
  name: my-svc
  namespace: my-ns
spec:
  ips:
  - 42.42.42.42
  type: "ClusterSetIP" # or "Headless"
  ports:
  - name: http
    protocol: TCP
    port: 80
  sessionAffinity: None
status:
  clusters:
  - cluster: us-west2-a-my-cluster
```

- All clusters containing the service's namespace will import the service by default.
  - all exporting clusters will also import the multi-cluster service
- If all `ServiceExport` instances are deleted, each `ServiceImport` will also be deleted from all clusters.
- The `ServiceImport.Spec.IP` (VIP) can be used to access this service from within this cluster.

#### ClusterSetIP

A non-headless ServiceImport is expected to have an associated IP address, the clusterset IP, which may be accessed from within an importing cluster. This IP may be a single IP used clusterset-wide or assigned on a per-cluster basis, but is expected to be consistent for the life of a ServiceImport from the perspective of the importing cluster. Requests to this IP from within a cluster will route to backends for the aggregated Service.

#### DNS

Optional, but recommended.

## GKE

### Multi Cluster Ingress (MCI)

- Deploying the app in multiple clusters
- Deploying `MultiClusterService` in config cluster

  ```yaml
  apiVersion: networking.gke.io/v1
  kind: MultiClusterService
  metadata:
    name: whereami-mcs
    namespace: whereami
  spec:
    template:
      spec:
        selector:
          app: whereami
        ports:
        - name: web
          protocol: TCP
          port: 8080
          targetPort: 8080
  ```

- `MultiClusterService` creates a derived **headless** `Service` in every cluster that matches.
- Why headless?
  - This service is not intended to be accessed directly by other services in the cluster.
  - pool member is pod IP
- Deploying `MultiClusterIngress` in config cluster

  ```yaml
  apiVersion: networking.gke.io/v1
  kind: MultiClusterIngress
  metadata:
    name: whereami-ingress
    namespace: whereami
  spec:
    template:
      spec:
        backend:
          serviceName: whereami-mcs
          servicePort: 8080
  ```

### Multi Cluster Services (MCS)

- sample app config: <https://tech.loveholidays.com/gke-multi-cluster-services-one-bad-probe-away-from-disaster-62051fafe84e>
- GCP have a managed CloudDNS service, which is use to configure DNS records for the MultiClusterService (not exposed in UI, run command `gcloud dns managed-zones list --location=us-central1-b` to see the zone)
- pod `gke-mcs-importer` is deployed in every cluster, collecting the endpoint, may configure the NEG (Network endpoint groups)
- with AutoPilot clusters, CloudDNS is used for service discovery (`kube-dns` is still there for fun)

## EKS

### Base on Cilium ClusterMesh [link](https://aws.amazon.com/blogs/containers/a-multi-cluster-shared-services-architecture-with-amazon-eks-using-cilium-clustermesh/)

### Base on AWS Cloud Map

- [repo](https://github.com/aws/aws-cloud-map-mcs-controller-for-k8s)
- Alpha phase, and NOT intended for production use

## Cilium Cluster Mesh

- use BGP to advertise routes and make Pod networks and/or Services reachable from outside the cluster for environments that support BGP

### [Service Global](https://docs.cilium.io/en/stable/network/clustermesh/clustermesh/)

- All clusters must be configured with the same datapath mode (Encapsulation or Native-Routing)
- PodCIDR ranges in all clusters and all nodes must be non-conflicting
- Nodes in all clusters must have IP connectivity between each other
- The network between clusters must allow the inter-cluster communication.

### [Multi-Cluster Services API (Beta)](https://docs.cilium.io/en/latest/network/clustermesh/mcsapi/)

- Installing CoreDNS multicluster

## Submariner

Submariner enables direct networking between Pods and Services in different Kubernetes clusters, either on-premises or in the cloud.

- Cross-cluster L3 connectivity using encrypted or unencrypted connections
- Service Discovery across clusters
- Support for interconnecting clusters with **overlapping CIDRs**

### Service Discovery

![Service Discovery](https://submariner.io/images/lighthouse/architecture.png)

- **Lighthouse Agent** runs in every cluster and accesses the Kubernetes API server running in the Broker cluster to exchange service metadata information with other clusters. Local Service information is exported to the Broker and Service information from other clusters is imported.
- Lighthouse DNS server runs as an external DNS server which owns the domain clusterset.local. CoreDNS is configured to forward any request sent to clusterset.local to the Lighthouse DNS server.

## If we want to provide this feature not depend on CNI (like GKE)

- need advertise the pod IP and service IP between clusters
  - `Calico` and `Cilium Overlay` ????
  - `Cilium Native Routing` ok
  - Global LB service can support all mode CNI, how can we do that?
- Deploy CRD `ServiceExport` and `ServiceImport`
- Install multi-cluster CoreDNS (or use external DNS)
- May run `mcs-controller` in management cluster to manage the `ServiceExport` and `ServiceImport`
- May run `mcs-importer` in every cluster to collect the endpoint or reduce the workload on the `mcs-controller`
