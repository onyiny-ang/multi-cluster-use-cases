# Multi-Cluster Use Cases

The aim of this repo is to demonstrate 4 major use cases of Kubernetes Federation as outlined below
and in the following [document](https://docs.google.com/presentation/d/1nBrqHu01aGkrXL94dCw9g8s01RsHKbhubuRDrdTXUP0/edit#slide=id.g23af27b1e2_0_212).

Steps for setting up and tearing down the most general/default federation control plane are described [here](https://kubernetes.io/docs/tasks/federation/set-up-cluster-federation-kubefed/) in great detail. The simplest way to set up federation is using kubefed with kubernetes version 1.5 or newer and kubefed version 1.6 or newer. The default is case 1.

## Statically Distribute an Application Consistently across all Clusters using the FC

A demonstration of the Federation Control Plane (FCP) being used to declaratively state that an application (a namespace and its contents) is to be deployed on every cluster under the FCP and that the FCP will then guarantee that the deployment happens in a manner consistent with the declared intent. The deployment is to be "consistent" in that the namespace and all its API objects and the amount of replicas are the same across every cluster.

## Statically Distribute an Application Varyingly across some clusters using the FCP
replica sets

## Statically Distribute an Application Varyingly across all clusters using the FCP

## Statically Distribute an Application Consistently across some Clusters using the FCP
