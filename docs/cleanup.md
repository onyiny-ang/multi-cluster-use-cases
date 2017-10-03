## Cleanup 

#### Unjoin Individual Clusters

If you joined each cluster individually by providing a unique name to each, then unjoin each one. Examples are given for GCE, AWS and Azure clouds service providers.

##### gce-us-west1

```bash
kubefed unjoin gce-us-west1 \
    --host-cluster-context=${HOST_CLUSTER}
```

##### az-us-central1

```bash
kubefed unjoin az-us-central1 \
    --host-cluster-context=${HOST_CLUSTER}
```

##### aws-us-east1

```bash
kubefed unjoin aws-us-east1 \
    --host-cluster-context=${HOST_CLUSTER}
```

#### Delete federation control plane

Cleanup of the federation control plane must be done manually for now. This removes everything except the persistent storage volume that is dynamically provisioned for the
federation control plane's etcd. You can delete the federation namespace by running the
following command in the correct context:

```bash
kubectl delete ns federation-system --context=${HOST_CLUSTER}
```

#### Delete the federation context

```bash
kubectl config use-context ${HOST_CLUSTER}
kubectl config delete-context federation
```

#### Delete clusters

Delete your remaining clusters, DNS managed zone and contexts as needed.


[Back to multi-cluster use cases](../README.md#multi-cluster-use-cases-1)
