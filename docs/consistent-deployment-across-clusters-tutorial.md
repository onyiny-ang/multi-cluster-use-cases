# Statically Distribute an Application Consistently across all Clusters using the FCP

The simplest way to set up federation is using kubefed with kubernetes version 1.5 or newer and kubefed version 1.6 or newer. Kubefed automates the process of statically distributing an application consistently across all clusters. This set up ensures that an application (a namespace and its contents) is deployed on every cluster under the FCP and that the FCP will then guarantee that the deployment happens in a manner consistent with the declared intent. The deployment is "consistent" in that the namespace and all its API objects and the amount of replicas are the same across every cluster.

Steps for setting up and tearing down the most general/default federation control plane are described [here](https://kubernetes.io/docs/tasks/federation/set-up-cluster-federation-kubefed/) in great detail.

[Consistent deployment across clusters tutorial](consistent-deployment-across-clusters/)

An simple example using GCE clusters can be followed [here](https://github.com/font/k8s-example-apps/blob/master/pacman-nodejs-app/docs/kubernetes-cluster-gke-federation.md#federated-kubernetes-tutorial)


