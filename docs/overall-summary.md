# Declarative Application Distribution Take Aways
#### The following findings were found through testing across 3 GKE clusters and with 1 GKE cluster and 1 AWS cluster

- YAML files--specifically ReplicaSet YAML files are the source of truth that allow for declarative application distribution

- Kubernetes uses loadbalancers to balance application distribution across clusters when the user does not specify how applications should be distributed. This does not guarantee the load is always balanced, with fluctuations happening--especially at start up and shutdown times. To ensure consistent deployment, min/max replicas must be specified for each cluster.

- Replicas can be easily deployed, consistently and varyingly across cluster or on specific clusters by altering min/max replica sets for each cluster

- There are 2 ways to indicate that a cluster should not be deployed to. It can be explicitly stated with min/max replicas set to 0 or the cluster can be left out of the annotation. Both methods seem to have the same effect.

- Once Replicas are set up on a multicluster, they can be changed through the `kubectl` command, `annotate` **It should be noted that this may change in future versions of kubectl/kubernetes**

- Annotations (or a similar feature) should be improved--a mistyped bracket or letter in the command will not fail or give any warning that an annotation was unsuccessful. Replicas will not be spread as desired but there will be no error or failed message to alert you to this.

- Both clusters attached to the FCP initially at the time of replica deployment, and those that are attached after the FCP is established with replicas/services can have replicas scheduled to them and away from them.  If an application is already deployed on a cluster that is then added to the FCP, any namespaces within that cluster that are also present in the FCP will be overwritten to reflect the specs in the FCP. Any namespaces within a cluster that are not present in the FCP will remain unchanged.

- Total number of replicas can be changed with the `kubectl scale` command

- The ease of declarative application distrubtion may be slightly complicated by adding persistent storage to an app running across a multicluster as federated persistent volumes are not currently supported and will not automatically communicate across clusters. DNS connections between clusters need to be set up manually in order for data to persistent across clusters in a federation at this time. This issue is out of the scope of this investigation.

- Unjoining clusters from the FCP without cleanup leaves them in the state they were last configured in by the FCP.

Back to [other use-cases](../README.md#multi-cluster-use-cases-1)


