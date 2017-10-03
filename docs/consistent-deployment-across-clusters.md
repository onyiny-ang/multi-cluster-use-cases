# Statically Distribute an Application Consistently across all Clusters using the FCP

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

## Deploy your app

As a simple example, we will be deploying nginx.

### Create the NGINX ReplicaSets

The replicasets/nginx.yaml file declares the number of replica sets desired in total across a federation. The number of replica sets will be spread out evenly amongst clusters (by default). Since we want to deploy an application consistently across all clusters, we will want to ensure that the number of replicasets we specify is a multiple of the total number of clusters we wish to deploy across. Thus, if you have 3 clusters and want one replica on each cluster, you would make the replicas: 3, if you would like 3 replica sets on each cluster, you would make the replicas: 9. In the following .yaml file, 3 replica sets will be deployed.

```
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  labels:
    name: nginx
  name: nginx
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
        name: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.10
          resources:
            requests:
              cpu: 100m
              memory: 100Mi
```
When you have altered the replicasets/nginx.yaml file appropriately, run

```
kubectl create -f replicasets/nginx.yaml
```

#### Verify your replica sets

Ensure that each of your replica sets is ready
 
```
kubectl get rs -o wide --watch
```

#### List Pods

You can ensure that the number of replicas you desire is running in each pod with the following command

```
CLUSTERS="name-1 name-2 name-3"
```

```
for cluster in ${CLUSTERS}; do
  echo ""
  echo "${cluster}"
  kubectl --context=${cluster} get pods
done
```

### Create NGINX Service

This component creates the necessary `nginx` federation DNS entries for each cluster. There will be A type DNS entries created for each zone, region, as well as a top level DNS A type entry that will resolve to all zones for load balancing.

```
kubectl create -f services/nginx-service.yaml
```

#### Verify

Wait and verify the service has all the external IP addresses listed:

```
kubectl get svc nginx -o wide --watch
```
#### List Services

```
for cluster in ${CLUSTERS}; do
  echo ""
  echo "${cluster}"
  kubectl --context=${cluster} describe services nginx
done
```

## NGINX

Once the `nginx` service has an IP address for each replica, open up your browser and try to access it via its
DNS e.g. [http://nginx.default.federation.svc.federation.com/](http://nginx.default.federation.svc.federation.com/). Make sure to replace `federation.com` with your DNS name.

## Cleanup

Clean up you federation by following [these steps](./cleanup.md)

Back to [other use-cases](../README.md#multi-cluster-use-cases)
