# Statically Distribute an Application Consistently across Some Clusters
 
As a user of multiple Kubernetes clusters, I want to ensure that a set of resources is running in the exact same way (number of replicas, size, scale) on selected clusters in a set, so that I can have copies of those resources created faithfully in those selected clusters.
 
This is a slightly more complicated multi-cluster use case, where a Kubernetes user wishes to distribute the exact same set of resources into some clusters joined in a multi-cluster. In this use case, the set of resources is frequently the contents of a Kubernetes namespace. The namespace name is the same in every cluster.
 
Upon setting up the federation control plane with one cluster designated as the host (using kubefed init), the kubefed command makes the realization of this use case fairly straight forward. Each cluster, even those that will not immediately be allocated resources, is joined to the federation control plane using the kubefed join command. Once this structure is set up, YAML files specifying the clusters that will be allocated resources should be written and deployed. Once the replica sets are established, resources will only be allocated to those clusters that have replicas. Any cluster without replicas will simply be idle.
For example:

```
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: nginx-us
  annotations:
    federation.kubernetes.io/replica-set-preferences: |
        {
            "rebalance": true,
            "clusters": {
                "cluster-1": {
                    "minReplicas": 2,
                    "maxReplicas": 2
                    "weight": 1
                },
                "cluster-3": {
                    "minReplicas": 2,
                    "maxReplicas": 2,
                    "weight": 1
                }
                etc. . .
            }
        }

```
It is also fairly straight forward to change which clusters are allocated resources after deployments by passing the proper arguments to kubectl as well as changing the amount of resources allocated to each cluster.

```
kubectl annotate rs/nginx federation.kubernetes.io/replica-set-preferences='{"rebalance": true, "clusters": {"cluster-1": {"minReplicas": 10, "maxReplicas": 10, "weight": 0},"cluster-2": {"minReplicas": 10, "maxReplicas": 10, "weight": 0}}}'
```


The apparent simplicity of this set up is complicated by persistent data storage. At this point there is no support for federated persistent data. The host cluster can be made with its own persistent volume that can be propagated out to each cluster but there is no automatic communication between these clusters or persistence across clusters. That is not to say that communication between clusters is impossible, just that it involves a few extra steps (example with mongodb on pacman: https://github.com/font/k8s-example-apps/blob/master/pacman-nodejs-app/docs/pacman-nodejs-app-federated-multicloud.md#create-mongodb-persistent-volume-claims).

Static distribution of an application can be deployed consistently across multiple cloud providers which helps customers to avoid vendor lock-in.

[Back to multi-cluster use cases](../README.md#multi-cluster-use-cases-1)

