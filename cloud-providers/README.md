# Kubernetes Cluster Setup

<https://kubernetes.io/docs/setup/>
<https://kubernetes.io/docs/concepts/cluster-administration/cluster-administration-overview/>

## ✊ Goals

In this section we will deploy several Kubernetes clusters. The goal is to create a production-grade, easy-to-use, easy-to-monitor deployment system, and deploy a few "Hello, world!" apps onto the cluster.

## Comparing cloud providers

After a couple of days of fiddling with GKE, AWS, Azure ACS (and getting a glance of AKS), that's the impression:

- Terraform + GKE seems to offer the most configuration simplicity and a clean declarative workflow.
- Azure's ACS is a direct competitor to GKE, and seems to have great potential as well. AKS (the new ACS) is not yet supported by Terraform.
- AWS lacks a native Kubernetes solution. `kops` is the "official" way to host a Kubernetes cluster on AWS, and <https://engineering.bitnami.com/articles/how-bitnami-uses-kops-to-manage-kubernetes-clusters-on-aws.html> offers a glance into a smooth workflow for hosting a Kubernetes cluster on AWS.

## The plan

1. Declaratively (via 1 command) create a cluster and deploy all supporting tools (Ingress + Docker image registry + Jenkins / Drone).
2. Deploy a hello-world app.


## Ideal case to strive for

### Cloud providers

A cloud provider of our choice manages all hardware (data centers). It also monitors compute / memory / storage resource pressure and auto-scales VMs when needed. It's designed for ~100% availability and ~100% durability.

On our part (as a user of a cloud provider), we manage a cluster git repository and handle all required changes to the cluster state by making changes to cluster files in the repository. Changes are then handled via a CI pipeline (git-based workflow, either *git-flow* or *GitHub Flow*):

- Commit change
- Run all tests / checks to make sure everything works and is ready to be deployed
- Deploy into production if checks are successful

### Goals

A cluster should define a **logical boundary** and not constrained by any technical requirement. It's fine for clusters to be large (e.g. a company cluster).

Every cluster has its codebase. A company should always have a **staging cluster** to allow DevOps engineers to test cluster configuration.

### Cluster updates

Updates to VMs should **always** be rolling and the update process should be abstracted from the DevOps engineers. Want a new version of Kubernetes? Just bump the version in cluster configuration repo and commit changes. Let the tooling (CI + cloud provider interface) to handle the rest.