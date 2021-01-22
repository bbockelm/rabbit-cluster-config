
Flux repository for the Rabbit Cluster
======================================

Installation
------------

After launching the Rabbit cluster on AWS, configure `kubectl` to talk to rabbit
with the following command:

```
aws eks update-kubeconfig --name rabbit
```

Next, bootstrap the flux instance:

```
kubectl apply -k manifests/production
```

On first start, flux will generate and install a new SSH deploy key.  You
can retrieve the key with the following:

```
fluxctl identity --k8s-fwd-ns flux
```

You must add this deploy key to the GitHub repo.
