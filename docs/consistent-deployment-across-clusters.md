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

#### Create the Nginx ReplicaSets

The replicasets/nginx.yaml file declares the number of replicas we desire. This can be altered as desired. With the file as is, 3 replica sets will be deployed.

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

####Nginx Service

This component creates the necessary `nginx` federation DNS entries for each cluster. There will be A DNS entries created for each zone, region,
as well as a top level DNS A entry that will resolve to all zones for load balancing.

```
kubectl create -f services/nginx-service.yaml
```

Wait and verify the service has all the external IP addresses listed:

```
kubectl get svc nginx -o wide --watch
```

#### Create the Nginx Deployment

We'll need to create the Nginx game deployment to access the application on port 80.

```
kubectl create -f deployments/nginx-deployment-rs.yaml
```

Scale the nginx deployment

```
kubectl scale deploy/nginx --replicas=3
```

Wait until the nginx deployment shows 3 pods available

```
kubectl get deploy nginx -o wide --watch
```

Once the `nginx` service has an IP address for each replica, open up your browser and try to access it via its
DNS e.g. [http://nginx.default.federation.svc.federation.com/](http://nginx.default.federation.svc.federation.com/). Make sure to replace `federation.com` with your DNS name.

You can also see all the DNS entries that were created in your [Google DNS Managed Zone](https://console.cloud.google.com/networking/dns/zones).

## Nginx

Go ahead and play a few rounds of Nginx and invite your friends and colleagues by giving them your FQDN to your Nginx application
e.g. [http://nginx.default.federation.svc.federation.com/](http://nginx.default.federation.svc.federation.com/) (replace `federation.com` with your DNS name).

The DNS will load balance and resolve to any one of the zones in your federated kubernetes cluster. This is represented by the `Zone:`
field at the top. When you save your score, it will automatically save the zone you were playing in and display it in the High Score list.

See who can get the highest score!

