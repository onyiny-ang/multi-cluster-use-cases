# Configure gcloud 

This tutorial will walk you through setting up a single kubernetes cluster using google cloud engine.
The same process can be used with the zone and name information changed to spin up extra gce clusters in different regions.

You should first configure gcloud to use client certificates:

```gcloud config set container/use_client_certificate True```

Next you can use the `gcloud container clusters create` command to create the Kubernetes clusters you want to use. The following is an example for setting up a cluster in us-west1-b. You will need to set up at least 2 clusters to demonstrate federation.

#### gce-us-west1

```
gcloud container clusters create gce-us-west1 \
  --zone=us-west1-b \
  --scopes "cloud-platform,storage-ro,logging-write,monitoring-write,service-control,service-management,https://www.googleapis.com/auth/ndev.clouddns.readwrite"
```

### Verify the clusters

You can verify the Kubernetes clusters that are running with:

```
gcloud container clusters list
```

### Store the GCP Project Name

```
export GCP_PROJECT=$(gcloud config list --format='value(core.project)')
```

## Create a Google DNS Managed Zone

The following command creates a DNS zone named `federation`. You should replace the dns-name with a valid managed zone backed by a registered DNS domain.

```
gcloud dns managed-zones create federation \
  --description "multi-cluster use case testing" \
  --dns-name federation.localdomain
```

Back to [Multi-Cluster setup](../README.md#download-install-and-configure-kubectl-and-kubefed)
