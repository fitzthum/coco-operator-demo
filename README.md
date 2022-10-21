# Confidential Containers Operator Demo

Confidential Containers (CoCo) can be installed on top of an existing Kubernetes cluster using the operator.

This guide is based on the official CoCo [quickstart guide](https://github.com/confidential-containers/documentation/blob/main/quickstart.md)

First, make sure you have a cluster with at least one worker node.
You must use `containerd` as your CRI runtime.

## Installing the operator

```
kubectl apply -f https://raw.githubusercontent.com/confidential-containers/operator/main/deploy/deploy.yaml
```

Wait until the operator pods have started.
```
kubectl get pods -n confidential-containers-system
```

```
kubectl apply -f https://raw.githubusercontent.com/confidential-containers/operator/main/config/samples/ccruntime.yaml
```

Check the system pods again
```
kubectl get pods -n confidential-containers-system
```

Once the operator has been installed you should have Kata runtime classes.
```
kubectl get runtimeclass
```

It might take a few minutes for installation to complete.
The output of the above command should be
```
NAME            HANDLER         AGE
kata            kata            9m55s
kata-clh        kata-clh        9m55s
kata-clh-tdx    kata-clh-tdx    9m55s
kata-qemu       kata-qemu       9m55s
kata-qemu-tdx   kata-qemu-tdx   9m55s
kata-qemu-sev   kata-qemu-sev   9m55s
```

To start a container with Confidential Containers, just use one of the above runtime classes.

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: bitnami/nginx:1.22.0
    name: nginx
  dnsPolicy: ClusterFirst
  runtimeClassName: kata
```

Create the above pod.
```
kubectl apply -f nginx-cc.yaml
```

Make sure that the pod has started.
```
kubectl get pods
```

You can use `crictl` to check whether the container image was downloaded on the host (it wasn't).
```
sudo crictl  -r  unix:///run/containerd/containerd.sock image ls | grep bitnami/nginx
```

