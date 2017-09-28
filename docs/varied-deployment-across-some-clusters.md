# Statically Distribute an Application Varyingly across some Clusters using the FCP

set up FCP or observer, first one, then the other
[example](https://github.com/font/k8s-example-apps/blob/master/pacman-nodejs-app/docs/pacman-nodejs-app-federated-aws-gke-portability.md)

I suspect this can be done simply on two clusters of the same type (GCE-GCE) and without persistant storage (mongo) otherwise there is some added complexity (migration tool takes care of this for pacman).
an we switch which cluster we deploy on at will?
deploy on whichever cluster (out of potentially thousands) we want
is this automated or programmed? should allow options for both.
 Deployments are declarative,


