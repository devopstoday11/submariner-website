+++
date = 2020-08-24T11:35:16+02:00
title = "Releases"
pre = "<b>6. </b>"
weight = 30
+++

## v0.6.0 Improved Submariner High Availability and various Lighthouse enhancements

> This release mainly focused on support for headless Services in Lighthouse,
> as well as improving Submariner's High Availability (HA).

{{% notice warning %}}
  The DNS domains have been updated from \<service\>.\<namespace\>.svc.**supercluster**.local to
  \<service\>.\<namespace\>.svc.**clusterset**.local to align with the change in Kubernetes MultiCluster Service API.
  Both domains will be supported for 0.6.0 but 0.7.0 will remove support for **supercluster**.local.
  Please update your deployments and applications.
{{% /notice %}}

* **Lighthouse** has been enhanced to:
  * Be aware of the local cluster Gateway connectivity so as not to announce the IP addresses for disconnected remote clusters.
  * Support headless Services for non-**Globalnet** deployments. Support for **Globalnet** will be available in a future release.
  * Be aware of a Service's backend Pods so as not to announce IP addresses for Services that have no active pods.
  * Use Round Robin IP resolution for services available in multiple clusters.
  * Enable Service discovery by default for **subctl** deployments.
* **subctl** auto-detects the cluster ID from the kubeconfig file information when possible.
* The Submariner Pods now shutdown gracefully and do proper cleanup which reduces the downtime during Gateway failover.
* The Operator now automatically exports Prometheus metrics; these integrate seamlessly with OpenShift Prometheus if user
  workload monitoring is enabled, and can be included in any other Prometheus setup.
* Minimum Kubernetes version is now 1.17.
* HostNetwork to remote Service connectivity fixes for AWS clusters ([Issue 736](https://github.com/submariner-io/submariner/issues/736)).
* The project's codebase quality and readability has been improved using various linters.

## v0.5.0 Lighthouse service discovery alignment

> This release mainly focused on continuing the alignment of Lighthouse's service discovery support with the [Kubernetes Multicluster
> Services KEP][MCS KEP].

* **Lighthouse** has been modified per the [Kubernetes Multicluster Services KEP][MCS KEP] as follows:
  * The `MultiClusterService` resource has been replaced by `ServiceImport`.
  * The `ServiceExport` resource is now updated with status information as lifecycle events occur.
* **Lighthouse** now allows a `ServiceExport` resource to be created prior to the associated `Service`.
* Network discovery was moved from `subctl` to the Submariner Operator.
* Several new commands were added to `subctl`: `export service`, `show versions`, `show connections`, `show networks`,
  `show endpoints`, and `show gateways`.
* The `subctl info` command has been removed in lieu of the new `show networks` command.
* The **Globalnet** configuration has been moved from the _broker-info.subm_ file to a `ConfigMap` resource stored on the
  broker cluster. Therefore, the new `subctl` cannot be used on brownfield **Globalnet** deployments where this information
  was stored as part of _broker-info.subm_.
* `subctl` now supports joining multiple clusters in parallel without having to explicitly specify the `globalnet-cidr` for the
  cluster to work around this issue. The `globalnet-cidr` will automatically be allocated by `subctl` for each cluster.
* The separate `--operator-image` parameter has been removed from `subctl join` and the `--repository` and `--version`
  parameters are now used for all images.
* The Submariner Operator status now includes `Gateway` information.
* Closed technical requirements for Submariner to become a CNFC project, including _Developer Certificate of Origin_ compliance,
  and source code linting.

## v0.4.0 Libreswan cable driver, Kubernetes multicluster service discovery

> This release is mainly focused on Submariner's Libreswan cable driver implementation, as well
> as standardizing Lighthouse's service discovery support with the [Kubernetes Multicluster
> Services KEP][MCS KEP].

* **Libreswan** IPsec cable driver is available for testing and is covered in Submariner's CI.
* **Lighthouse** has been modified per the [Kubernetes Multicluster Services KEP][MCS KEP] as follows:
  * A `ServiceExport` object needs to be created alongside any `Service` that is intended to be
    exported to participant clusters.
  * Supercluster services can be accessed with `<service-name>.<namespace>.svc.clusterset.local`.
* **Globalnet** overlapping CIDR support improvements and bug fixes.
* Multiple **CI** improvements implemented from Shipyard.
* CI tests are now run via **[GitHub Actions](https://github.com/submariner-io/submariner/actions)**.
* Submariner-operator now completely handles the Lighthouse deployment via the `ServiceDiscovery` CRD.
* `subctl verify` is now available for `connectivity`, `service-discovery` and `gateway-failover`.

[MCS KEP]: https://github.com/kubernetes/enhancements/tree/master/keps/sig-multicluster/1645-multi-cluster-services-api

## v0.3.0 Lighthouse Service Discovery without KubeFed

> This release is focused on removing the KubeFed dependency from Lighthouse, improving the user experience
> and adding experimental WireGuard support as an alternative to IPsec

* Lighthouse no longer depends KubeFed. All metadata exchange is handled over the Broker as MultiClusterService CRs.
* Experimental **Wireguard** support has been added as a pluggable CableDriver option in addition to the current default IPsec.
* Submariner reports the active and passive gateways as a gateway.submariner.io resource.
* The Submariner Operator reports a detailed status of the deployment.
* The **gateway redundancy/failover** tests are now enabled and stable in CI.
* *Globalnet hostNetwork* to remote globalIP is now supported. Previously, when a pod used hostNetworking it was unable to connect to a
  remote service via globalIP.
* A globalCIDR can be manually specified when joining a cluster with globalnet enabled. This enables CI speed optimizations via better
  parallelism.
* Operator and subctl are more robust via standard retries on updates.
* Subctl creates a new **individual access token** for every new joined cluster.

## v0.2.0 Overlapping CIDR support

> This release is focused on overlapping CIDR support between clusters

* Support for Overlapping CIDRs between clusters (globalnet)
* Enhanced e2e scripts, which will be shared between repositories in the shipyard project (ongoing work)
* Improved e2e deployment by using a local registry.
* Refactoring to support pluggable drivers (in preparation for [WireGuard](https://www.wireguard.com/) support)

## v0.1.1 Submariner with more light

> This release has focused on stability for the Lighthouse support

* Cleaner logging for the submariner-engine
* Cleaner logging for the submariner-route-agent
* Fixed issue with wrong token stored in subm file #244
* Added flag to disable the OpenShift CVO #235
* Fixed several service-discovery related bugs #194 , #167
* Fixed several panics on nil network discovery
* Added checks to ensure the CIDRs for joining cluster don't overlap with an existing ones.
* Fix context handling related to service-discovery / kubefed #180
* Use the right CoreDNS image for OpenShift.

## v0.1.0 Submariner with some light

> This release has focused on stability, bugfixes and making [Lighthouse](https://github.com/submariner-io/lighthouse) available as
> developer preview via subctl deployments.

* Several bugfixes and enhancements around HA failover (#346, #348, #332)
* Migrated to Daemonsets for submariner gateway deployment
* Added support for hostNetwork to remote pod/service connectivity (#288)
* Auto detection and configuration of MTU for vx-submariner, jumbo frames support (#301)
* Support for updated strongswan (#288)
* Better iptables detection for some hosts (#227)

> subctl and the submariner Operator have the following improvements

* support to verify-connectivity between two connected clusters
* deployment of submariner gateways based in daemonsets instead of deployments
* renaming submariner pods to "submariner-gateway" pods for clarity
* print version details on crash (subctl)
* stop storing IPSEC key on broker during deploy-broker, now it's only contained into the .subm file
* version command for subctl
* nicer spinners during deployment (thanks to kind)

## v0.0.3 -- KubeCon NA 2019

Submariner has been greatly enhanced to allow Operators to deploy into Kubernetes clusters without the necessity for layer-2 adjacency for
nodes. Submariner now allows for VXLAN interconnectivity between nodes (facilitated by the route agent). Subctl was created to make
deployment of submariner easier.

## v0.0.1 Second Submariner release

## v0.0.1 First Submariner release
