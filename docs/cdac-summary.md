# Statically Distribute an Application Consistently across all Clusters
 
As a user of multiple Kubernetes clusters, I want to ensure that a set of resources is running in the exact same way (number of replicas, size, scale) on every cluster in a set, so that I can have copies of those resources created faithfully in different clusters.
 
This is essentially the simplest multi-cluster use case, where a Kubernetes user wishes to distribute the exact same set of resources into multiple clusters. In this use case, the set of resources is frequently the contents of a Kubernetes namespace. The namespace name is the same in every cluster.
 
Upon setting up the federation control plane with one cluster designated as the host (using kubefed init), the kubefed command makes the realization of this use case very straight forward. Each cluster is joined to the federation control plane using the kubefed join command. Once this structure is set up, all deployments made on the host cluster are cascaded to each of the joined clusters such that each cluster is ostensibly a clone of the host. The exception lies with replica sets. The number of replica sets that are indicated are spread across clusters. Replicas are supposed to be balanced across clusters (so if you have 3 clusters and want 9 replicas, there will tend to be 3 replicas on each cluster) but it was found through testing that this is not always what happens with replicas being slightly unbalanced--at least momentarily--across clusters. The best way to ensure that the same number of replicas are placed on each cluster is to explicitly state the min and max replicas in a replica yaml file and create the replicas that way.  
For example:

```
piVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: nginx-us
  annotations:
    federation.kubernetes.io/replica-set-preferences: |
        {
            "rebalance": true,
            "clusters": {
                "us-east1-b": {
                    "minReplicas": 2,
                    "maxReplicas": 2
                    "weight": 1
                },
                "us-central1-b": {
                    "minReplicas": 2,
                    "maxReplicas": 2,
                    "weight": 1
                }
                etc. . .
            }
        }

```
Another complication that was not examined here is persistent data storage. At this point there is no support for federated persistent data. The host cluster can be made with its own persistent volume that can be propagated out to each cluster but there is no automatic communication between these clusters or persistence across clusters. That is not to say that communication between clusters is impossible, just that it involves a few extra steps (example with mongodb on pacman: https://github.com/font/k8s-example-apps/blob/master/pacman-nodejs-app/docs/pacman-nodejs-app-federated-multicloud.md#create-mongodb-persistent-volume-claims).

Static distribution of an application can be deployed consistently across multiple cloud providers which helps customers to avoid vendor lock-in.

[Back to multi-cluster use cases](../README.md#multi-cluster-use-cases)

