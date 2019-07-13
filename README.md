# k8s-atlantico

## Pre-requisites
You need to have a Kubernetes Cluster and the kubectl command-line tool must be configured to communicate with your cluster. 
In this case, we are using [Minikube](https://kubernetes.io/docs/setup/minikube) as cluster.

## Enable the Ingress Controller
Ingress is a collection of routing rules that govern how external users access services running in a Kubernetes cluster.

1. Enable the NGINX Ingress controller:
```
$ minikube addons enable ingress
```

2. Verify that the NGINX Ingress controller is running:
```
$ kubectl get pods -n kube-system
```
Output:

    NAME                                        READY   STATUS    RESTARTS   AGE
    etcd-minikube                               1/1     Running   0          10h
    kube-addon-manager-minikube                 1/1     Running   0          10h
    kube-apiserver-minikube                     1/1     Running   0          10h
    kube-controller-manager-minikube            1/1     Running   0          10h
    kube-proxy-bzxtm                            1/1     Running   0          10h
    kube-scheduler-minikube                     1/1     Running   0          10h
    nginx-ingress-controller-7b465d9cf8-2pm6j   1/1     Running   0          10h
    storage-provisioner                         1/1     Running   0          10h


## Create an Ingress resource
The `ingress.yaml` file is an Ingress resource that sends traffice to your service via hello-world.info.

1. Create the Ingress resource by running the following command:
```
$ kubectl apply -f ingress.yaml
```

Output:

    ingress.networking.k8s.io/example-ingress created

2. Verify the IP address is set (This can take a couple of minutes):
```
$ kubectl get ingress
````
Output:

    NAME              HOSTS              ADDRESS     PORTS   AGE
    example-ingress   hello-world.info   10.0.2.15   80      10h

As you can see, the IP address from Ingress is the internal IP provided by Kubernetes Cluster.
We need to get the IP address which is configure to route to ingress.

```
$ minikube ip
```
Output:

    192.168.99.104

3. Add the following line to the bottom of the **/etc/hosts** file:

    `192.168.99.104 hello-world.info`

### Deploy a hello, world app
The `pod.yaml` file uses a publicly available hello-world image from Docker Hub.
Note the hello-world service's type is ClusterIP, because this service will be proxied by Ingress. The hello-world doesn't need public access directly to it.

1. Create the hello-world resource by running the follow command:"
```
$ kubectl create -f pod.yaml
```

2. Verify that the Ingress controller is directing traffic:
```
$ curl hello-world.info
```

Output:

    Hello, world!
    Version: 1.0.0
    Hostname: web

