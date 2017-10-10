# Statically Distribute an Application Consistently across all Clusters
 
As a user of multiple Kubernetes clusters, I want to ensure that a set of resources is running in the exact same way (number of replicas, size, scale) on every cluster in a set, so that I can have copies of those resources created faithfully in different clusters.
 
Upon setting up the federation control plane with one cluster designated as the host (using kubefed init), the kubefed command makes the realization of this use case very straight forward. Each cluster is joined to the federation control plane using the kubefed join command. Once this structure is set up, YAML files will be used as the source of truth to determine how an application is distributed across clusters. A different YAML file will be required for each application but once the YAMLs are set up correctly, it is very easy to manipulate how and in what quantity apps are cascaded to clusters in the multicluster. 

Replica sets are _supposed_ to be balanced across clusters (so if you have 3 clusters and want 9 total replicas, there will tend to be 3 replicas on each cluster) but it was found through testing that **this is not always what happens** with replicas being slightly unbalanced--at least momentarily--across clusters. The best way to ensure that the same number of replicas are placed on each cluster is to explicitly state the min and max replicas in a YAML file and create the replicaset that way.  

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
                "cluster-2": {
                    "minReplicas": 2,
                    "maxReplicas": 2,
                    "weight": 1
                }
                etc. . .
            }
        }

```
**Disclaimer: Annotations are being phased out and may no longer be supported in this way in the future**

Another complication that was not examined here is persistent data storage. At this point there is no support for federated persistent data. The host cluster can be made with its own persistent volume that can be propagated out to each cluster but there is no automatic communication between these clusters or persistence across clusters. That is not to say that communication between clusters is impossible, just that it involves a few extra steps (example with mongodb on pacman: https://github.com/font/k8s-example-apps/blob/master/pacman-nodejs-app/docs/pacman-nodejs-app-federated-multicloud.md#create-mongodb-persistent-volume-claims).

[Back to multi-cluster use cases](../README.md#multi-cluster-use-cases-1)

