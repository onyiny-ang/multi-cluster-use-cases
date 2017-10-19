# Statically Distribute an Application Varyingly across Some Clusters
 
As a user of multiple Kubernetes clusters, I want to ensure that a set of resources  is running in a specific manner, unique to each cluser (ie. a varied number of replicas, size, scale) on selected clusters in a set, so that I can customize how my resources are spread out and created in exactly the way I have indicated in those selected clusters.
 
Upon setting up the federation control plane with one cluster designated as the host (using kubefed init), the kubefed command makes the realization of this use case farily straight forward. Each cluster is joined to the federation control plane using the kubefed join command. Once this structure is set up,YAML files will be used as the source of truth to determine how an application is distributed across clusters. A different YAML file will be required for each application but once the YAMLs are set up correctly, it is very easy to manipulate how and in what quantity apps are cascaded to clusters in the multicluster. In this particular use case, any cluster without replicas will simply be idle.

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
                    "minReplicas": 3,
                    "maxReplicas": 3,
                    "weight": 1
                },
                "cluster-3": {
                    "minReplicas": 1,
                    "maxReplicas": 1,
                    "weight": 1
                }
                etc. . .
            }
        }
spec:
  replicas: 4

etc. . .


```
**Disclaimer: Annotations are being phased out and may no longer be supported in this way in the future**

It is possible to further customize the weighing of replicas on a specific cluster by changing the min/max Replicas and the weight given to each cluster. It is also possible to change which cluster are allocated resources and how those resources are allocated with the following `kubectl` command.

```
kubectl scale replicas=12 rs/nginx

kubectl annotate rs/nginx federation.kubernetes.io/replica-set-preferences='{"rebalance": true, "clusters": {"cluster-1": {"minReplicas": 10, "maxReplicas": 10, "weight": 0},"cluster-2": {"minReplicas": 2, "maxReplicas": 2, "weight": 0}}}'
```

Another complication that was not examined here is persistent data storage. At this point kubefed init does not support attaching an existing PersistentVolumeClaim to the FCP. PVCs can be deployed to each of the clusters attached to the FCP but this must be done manually and there is no automatic communication between the PVCs or persistence across clusters. That is not to say that communication between clusters is impossible, just that it involves a few extra steps (example with mongodb on pacman: https://github.com/font/k8s-example-apps/blob/master/pacman-nodejs-app/docs/pacman-nodejs-app-federated-multicloud.md#create-mongodb-persistent-volume-claims).

[Back to multi-cluster use cases](../README.md#multi-cluster-use-cases-1)

