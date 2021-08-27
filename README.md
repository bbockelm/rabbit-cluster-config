
Flux repository for the Rabbit Cluster
======================================

Installation
------------

Navigate to the AWS EKS console and create the cluster through the web interface; name it `rabbit`
and ensure that you're doing this as a user with web _and_ programmatic access (may not be the default).

Utilize the `t3.medium` instance in the node group to ensure sufficient memory to run the cluster.

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

For sealed secret use, the public key generated for the cluster can be generated via:

```
kubectl get secret -n kube-system -o yaml -l sealedsecrets.bitnami.com/sealed-secrets-key | grep tls.crt | awk '{print $NF;}' | base64 --decode > kubeseal.pem
```

Authenticating Users
--------------------

To add an existing user to the Kubernetes cluster, edit the `aws-auth` configmap as follows (assuming we
want to make user `matyas-robot` a superuser on the cluster:

```
apiVersion: v1
kind: ConfigMap
data:
  mapRoles: |
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::XXXXXXXXXXXX:role/EKSWorkerNodes
      username: system:node:{{EC2PrivateDNSName}}
  mapUsers: |
    - userarn: arn:aws:iam::XXXXXXXXXXXX:user/matyas-robot
      username: matyas
      groups:
        - system:masters
```

Replace `XXXXXXXXXXXX` as necessary to make the ARN correct (the correct `rolearn` should exist
at cluster startup.
