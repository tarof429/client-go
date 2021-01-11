# Authenticating inside the cluster

This example shows you how to configure a client with client-go to authenticate
to the Kubernetes API from an application running inside the Kubernetes cluster.

client-go uses the [Service Account token][sa] mounted inside the Pod at the
`/var/run/secrets/kubernetes.io/serviceaccount` path when the
`rest.InClusterConfig()` is used.

## Running this example

First compile the application for Linux:

    cd in-cluster-client-configuration
    docker build -t in-cluster .

If you are running a [Minikube][mk] cluster, you can build this image directly
on the Docker engine of the Minikube node without pushing it to a registry. To
build the image on Minikube:

    cd in-cluster-client-configuration
    eval $(minikube docker-env)
    docker build -t in-cluster .

If you are not using Minikube, you should build this image and push it to a registry
that your Kubernetes cluster can pull from.

If you have RBAC enabled on your cluster, use the following
snippet to create role binding which will grant the default service account view
permissions. This is the case with Minikube by default; if you try to run the pod and it fails with a forbidden error, then RBAC is enabled.

```
kubectl create clusterrolebinding default-view --clusterrole=view --serviceaccount=default:default
```

Then, run the image in a Pod with a single instance Deployment:

    kubectl apply -f in-cluster-pod.yaml
    kubectl logs demo
    There are 8 pods in the cluster
    Pod example-xxxxx not found in default namespace
    ...

The example now runs on Kubernetes API and successfully queries the number of
pods in the cluster every 10 seconds.

### Clean up

To stop this example and clean up the pod, press <kbd>Ctrl</kbd>+<kbd>C</kbd> on
the `kubectl run` command and then run:

    kubectl delete -f in-cluster-pod.yaml

[sa]: https://kubernetes.io/docs/admin/authentication/#service-account-tokens
[mk]: https://kubernetes.io/docs/getting-started-guides/minikube/
