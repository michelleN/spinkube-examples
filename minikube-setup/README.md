# Installing SpinKube on Minikube

## Pre-requisites

Have Minikube installed on machine. On mac: `brew install minikube`
Start minikube with containerd as container runtime:

```console
minikube start --container-runtime=containerd
```

## Install SpinKube

1. Install containerd-shim-spin binary on cluster using kwasm

   _Note: kwasm should be replaced with runtime-class-manager but the Helm chart for that project is still in progress: [PR #132](https://github.com/spinkube/runtime-class-manager/pull/132)_

   ```console
   helm repo add kwasm http://kwasm.sh/kwasm-operator/
   helm install kwasm-operator kwasm/kwasm-operator --namespace kwasm --create-namespace --set kwasmOperator.installerImage=ghcr.io/spinkube/containerd-shim-spin/node-installer:v0.15.1
   kubectl annotate node --all kwasm.sh/kwasm-node=true
   ```

2. Install cert-manager

   Cert-manager is a spin-operator dependency.

   ```console
   helm install \
    cert-manager jetstack/cert-manager \
    --namespace cert-manager \
    --create-namespace \
    --version v1.14.5 \
    --set installCRDs=true
   ```

3. Install spin-operator

   ```console
   kubectl apply -f https://github.com/spinkube/spin-operator/releases/download/v0.2.0/spin-operator.runtime-class.yaml
   kubectl apply -f https://github.com/spinkube/spin-operator/releases/download/v0.2.0/spin-operator.crds.yaml
   helm install spin-operator --namespace spin-operator --create-namespace --version 0.2.0 --wait oci://ghcr.io/spinkube/charts/spin-operator
   kubectl apply -f https://github.com/spinkube/spin-operator/releases/download/v0.2.0/spin-operator.shim-executor.yaml
   ```

   _Note: If the helm install gives you trouble, try running the same command but replace "install" with "upgrade". More info on that to come._

4. Install sample application

   ```console
   kubectl apply -f https://raw.githubusercontent.com/spinkube/spin-operator/main/config/samples/simple.yaml
   kubectl port-forward services/simple-spinapp 8080:80
   curl localhost:8080/hello
   ```
