# Statically Distribute an Application Consistently across all Clusters using the FCP

The simplest way to set up this use case is using kubefed with kubernetes version 1.5 or newer and kubefed version 1.6 or newer. Kubefed automates the process of statically distributing an application consistently across all clusters. This set up ensures that an application (a namespace and its contents) is deployed on every cluster under the FCP and that the FCP will then guarantee that the deployment happens in a manner consistent with the declared intent. The deployment is "consistent" in that the namespace and all its API objects and the amount of replicas are the same across every cluster.

Assuming you have your kubernetes cluster running and federation control plane set up already:

## Add clusters to your federation

