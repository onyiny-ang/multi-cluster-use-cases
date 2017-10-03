# Statically Distribute an Application Consistently across Specific Clusters using the FCP

The simplest way to set up this use case is using kubefed with kubernetes version 1.5 or newer and kubefed version 1.6 or newer. Kubefed automates the process of statically distributing an application consistently across all clusters. This set up ensures that an application (a namespace and its contents) is deployed on every cluster under the FCP and that the FCP will then guarantee that the deployment happens in a manner consistent with the declared intent. The deployment is "consistent" in that the namespace and all its API objects and the amount of replicas are the same across every cluster.

Assuming you have your kubernetes cluster running and federation control plane set up already:

## Join clusters to your federation

Before proceeding, make sure we're using the newly created `federation` context to run our `kubefed join` commands.

```
kubectl config use-context federation
```

The `kubefed join` command joins each of the Kubernetes clusters from each zone. It is necessary to specify the host-cluster-context of the federation as well as the cluster-context of the kubernetes cluster that is being joined to the federation.

#### Use federation context
In this example, we're joining a GCE cluster in the east1 zone to a federation that has a GCE host cluster from west 1 zone. The cluster names and host-cluster names in the example below should be altered to match the names you have set for your clusters:

```
kubefed join gce-us-east1 \
    --host-cluster-context=gke_${GCP_PROJECT}_us-west1-b_gce-us-west1 \
    --cluster-context=gke_${GCP_PROJECT}_us-east1-b_gce-us-east1
```

Continue joining each of the clusters you wish to federate in this way.

#### Verify

```
kubectl get clusters -w
```


[Back to multi-cluster use cases](../README.md#multi-cluster-use-cases-1)

