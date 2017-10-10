# Declarative Application Distribution Take Aways

- YAML files--specifically ReplicaSet YAML files are the source of truth that allow for declarative application distribution

- Kubernetes uses loadbalancers to balance application distribution across clusters when the user does not specify how applications should be distributed. This does not guarantee the load is always balanced, with fluctuations happening--especially at start up and shutdown times. To ensure consistent deployment, min/max replicas must be specified for each cluster.

- Replicas can be easily deployed, consistently and varyingly across cluster or on specific clusters by altering min/max replica sets for each cluster

- Once Replicas are set up on a multicluster, they can be changed through the `kubectl` command, `annotate` **It should be noted that this may change in future versions of kubectl/kubernetes**

- Total number of replicas can be changed with the `kubectl scale` command

- Federated control plane creates the multicluster --test whether clusters can be added after the fact

- The ease of this process may be slightly complicated by adding persistent storage to an app running across a multicluster as DNS connections between clusters need to be set up manually at this time. This issue is out of the scope of this investigation.


