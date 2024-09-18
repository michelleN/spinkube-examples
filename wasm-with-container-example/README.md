# Running containers along side wasm example

## Pre-requisites

1. Have SpinKube running on a Kubernetes cluster. See minikube installation instructions [here](../minikube-setup/README.md).
2. Install sample app. Install instructions [here](../minikube-setup/README.md#install-sample-application).

## Add container to sample app deployment manifest

1. Edit the simple-spinapp deployment

   ```console
   kubectl edit deploy/simple-spinapp
   ```

2. Add the following section to the deployment manifest:

   ```yaml
   spec:
     containers:
     - name: hello-world-container
       image: busybox
       command: ['sh', '-c', 'while true; do echo "Hello, World!" && sleep 3600; done']
       ports:
       - containerPort: 8080
   ```

3. Check output of the pod to ensure container is running

   ```console
   $ kubectl logs <simple-spinapp-pod>
   "Hello, World!"
   ```
