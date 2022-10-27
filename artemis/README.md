# Kustomize manifests for deploying ActiveMQ Artemis operator to cluster

This folder contains Kubernetes Kustomize manifests that can be used to deploy
ActiveMQ Artemis operator to a Kuberntes cluster under namespace `passport-status`.

## ArtemisCloud.io introduction

https://artemiscloud.io/docs/getting-started/introduction/

The operator Tag v1.0.5 is used.

https://github.com/artemiscloud/activemq-artemis-operator/tree/v1.0.5

## Requirements

- A local Kubernetes cluster (ie: k3d, k3s, minikube, kind, etc).
- A recent version of `kubectl` and a `kubeconfig` file that can connect to your cluster.

## Deploying the operator using `kubectl`

The CRD definitions need to be install to the `default` namespace.

```sh
kubectl create -f ./crds
```

You can deploy the operator using the following commands:

```sh
kubectl apply --kubeconfig {path-to-your-kubeconfig} --kustomize ./artemis/ --namespace passport-status
```

## Appendix
