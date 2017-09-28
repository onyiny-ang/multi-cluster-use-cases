# Multi-Cluster Use Cases

The aim of this repo is to demonstrate 4 major use cases of Kubernetes Federation as outlined below
and in the following [document](https://docs.google.com/presentation/d/1nBrqHu01aGkrXL94dCw9g8s01RsHKbhubuRDrdTXUP0/edit#slide=id.g23af27b1e2_0_212).

The official kubernetes documentation does provide very [thorough documentation](https://kubernetes.io/docs/tasks/federation/set-up-cluster-federation-kubefed/) to set up/tear down a default federation which can be applied to any cluster. This most closely matches the first listed use case. This repo will attempt to simplify those instructions and provide a simple app to demonstrate the effectiveness of each use case.

## Prerequisites

### Set up kubernetes cluster(s)
In order to federate kubernetes clusters, one first needs to have kubernetes clusters running. There are [several solutions](https://kubernetes.io/docs/setup/pick-right-solution/) available to run a kubernetes cluster and these instructions should work similarly for any of the listed use cases. For convenience's sake, this tutorial will be using [Google Cloud Platform](https://cloud.google.com/container-engine/) to spin up and tear down kubernetes clusters.
We also need to set up a cluster DNS managed zone to manage external DNS entries based on services created across a federated set of Kubernetes clusters. This tutorial will use GCE for this as well 

Instructions to spin up a simple GCE cluster and create a google DNS managed zone can be found [here](docs/simple-gcloud-cluster.md)

### Download, install and configure kubectl and kubefed
Exactly how you set up kubectl will depend on the type of cluster you are using. 

#### To Download/Install:
Replace the version string with whatever version you want in the `curl` command below.

```
curl -O https://storage.googleapis.com/kubernetes-release/release/v1.6.4/kubernetes-client-linux-amd64.tar.gz
tar -xzvf kubernetes-client-linux-amd64.tar.gz kubernetes/client/bin/kubefed
tar -xzvf kubernetes-client-linux-amd64.tar.gz kubernetes/client/bin/kubectl
sudo cp kubernetes/client/bin/kubefed /usr/local/bin
sudo chmod +x /usr/local/bin/kubefed
sudo cp kubernetes/client/bin/kubectl /usr/local/bin
sudo chmod +x /usr/local/bin/kubectl
```

Ensure that `kubectl` is configured with each of the contexts you set up by listing the contexts stored in your local kubeconfig.


```
kubectl config get-contexts --output=name
```

### Initialize the Federated Control Plane

Initialization is easy with the `kubefed init` command. One of the kubernetes clusters you create must be the designated controller of the FCP. To give an example of the command, we will use the us-west region to host our federated control plane. Replace the `host-cluster-context` and `--dns-zone-name` parameters to match the context and DNS zone names you have used.

**Be sure to include the trailing `.` in the DNS zone name**.

`kubefed init` will set some defaults if you do not override them on the command line.
For example, you can pass `--image='gcr.io/google_containers/hyperkube-amd64:v1.6.4'`
to specify a different version of the federation API server and controller manager.
By default, the image version it pulls will match the version of `kubefed` you are
using i.e. `v1.6.4` in this case.

```
kubefed init federation \
    --host-cluster-context=gke_${GCP_PROJECT}_us-west1-b_gce-us-west1 \
    --dns-provider='google-clouddns' --dns-zone-name=federation.com.
```

Once the command completes, you will have a federated API server and controller-manager running as well as a `federation` context for `kubectl` commands.

Follow the instructions in the below links to test out different multi-cluster use-cases.

- [Statically Distribute an Application Consistently across all Clusters using the FCP](docs/consistent-deployment-across-clusters.md)
- [Statically Distribute an Application Consistently across some Clusters using the FCP](docs/consistent-deployment-across-some-clusters.md)
- [Statically Distribute an Application Varyingly across some Clusters using the FCP](docs/varied-deployment-across-some-clusters.md)
- [Statically Distribute an Application Varyingly across all clusters using the FCP](docs/varied-deployment-across-clusters.md)



