# sandbox-kubernetes

## Overview
**sandbox-kubernetes** is a set of Docker and Kubernetes configuration files used as a starting point for learning and testing key features of Kubernetes.

## Table of Contents

* [Docker Commands](#docker-commands)
* [Kubernetes Commands](#kubernetes-commands)
    * [General](#general)
    * [Labels](#labels)
    * [Annotations](#annotations)
    * [Namespaces](#namespaces)
    * [Replication Controllers](#replication-controllers)
    * [Replication Set](#replication-sets)
    * [Services](#services)
    * [Volumes](#volumes)

## Docker Commands

#### Build the container images
`docker build -t sandbox-k8s-app-image .`

#### Run the image
`docker run --name sandbox-k8s-app-container -p 8080:8080 -d sandbox-k8s-app-image`

#### Test the app
`curl localhost:8080`

#### Connect to running container
`docker exec -it sandbox-k8s-app-container bash`

#### Access logs from container
`docker logs {container id}`

#### Stop the container
`docker stop sandbox-k8s-app-container`

#### Remove the container
`docker rm sandbox-k8s-app-container`

#### Login to Docker Hub
`docker login`

#### Tag image
`docker tag sandbox-k8s-app-image jlotz/sandbox-k8s-app-image`

_Note: Replace "jlotz" with your Docker Hub ID_

#### Push to Docker Hub
`docker push jlotz/sandbox-k8s-app-image`

#### Run image from a different machine
`docker run -p 8080:8080 -d jlotz/sandbox-k8s-app-image`

## Kubernetes Commands

These instructions assume that you have a working installation of `minikube` running locally and the `kubectl` tools installed. 

### General

#### Start/stop cluster
`minikube start`
`minikube stop`

#### Open the minikube dashboard
`minikube dashboard`

#### Verify cluster is working
`kubectl cluster-info`

#### Deploy pod from YAML
`kubectl create -f sandbox-k8s-app-pod.yaml`

Specifying a namespace:

`kubectl create -f sandbox-k8s-app-pod.yaml -n sandbox-k8s-custom-namespace`

#### List pods using labels
`kubectl get po --show-labels`

`kubectl get po -L creation_method,env`

All pods with creation_method=manual:

`kubectl get po -l creation_method=manual`

All pods with env (with or without it)

`kubectl get po -l env`

No env:

`kubectl get po -l '!env'`

Similarly, you could also match pods with the following label selectors:

`creation_method!=manual` to select pods with the creation_method label with any value other than manual

`env in (prod,devel)` to select pods with the env label set to either prod or devel

`env notin (prod,devel)` to select pods with the env label set to any value other than prod or devel

#### Describe the pod
`kubectl describe pod sandbox-k8s-app-pod`

#### Get the full YAML or JSON for the pod
`kubectl get po sandbox-k8s-app-pod -o yaml`
`kubectl get po sandbox-k8s-app-pod -o json`

#### Access logs from pod
Single container:

`kubectl logs sandbox-k8s-app-pod`

Multiple containers (specify pod and container names):

`kubectl logs sandbox-k8s-app-pod sandbox-k8s-app-container`

Previous iteration (after a failure/restart):

`kubectl logs sandbox-k8s-app-pod --previous`

#### Setup port forwarding
`kubectl port-forward sandbox-k8s-app-pod 8888:8080`

#### Test app
`curl localhost:8888`

Or, open web browser to http://localhost:8888

#### Delete the pod
`kubectl delete po sandbox-k8s-app-pod`

You can also delete more than one pod by specifying multiple, space-separated names:

`kubectl delete po pod1 pod2`

Using label selectors:

`kubectl delete po -l creation_method=manual`

All pods in a namespace:

`kubectl delete ns sandbox-k8s-custom-namespace`

All pods in the current namespace:

`kubectl delete po --all`

All resources in the current namespace:

`kubectl delete all --all`

### Labels

#### Updating labels
`kubectl label po sandbox-k8s-app-pod env=test --overwrite`

### Annotations

#### Adding annotations
`kubectl annotate pod sandbox-k8s-app-pod mycompany.com/someannotation="foo bar"`

### Namespaces

#### Get namespaces
`kubectl get ns`

#### Get pods in a namespace
`kubectl get po -n kube-system`

#### Create a namespace
From a YAML file:

`kubectl create -f custom-namespace.yaml`

From the command line:

`kubectl create namespace custom-namespace`

#### Switch to a namespace
`kubectl config set-context $(kubectl config current-context) --namespace custom-namespace`

#### Verify current namespace
`kubectl config get-contexts`

### Replication Controllers

#### Create replication controller
`kubectl create -f sandbox-k8s-rc.yaml`

#### Get replication controllers
`kubectl get rc`

#### Describe replication controller
`kubectl describe rc sandbox-k8s-rc`

#### Delete replication controller
`kubectl delete rc sandbox-k8s-rc`

### Replication Sets

#### Create replication set
`kubectl create -f sandbox-k8s-rs.yaml`

#### Get replications sets
`kubectl get rs`

#### Describe replications sets
`kubectl describe rs sandbox-k8s-rs`

#### Delete replication sets
`kubectl delete rs sandbox-k8s-rs`

### Services

The following section assumes that you have deployed pods via replication sets as instructed above.

#### Create service
ClusterIP (Default):

`kubectl create -f sandbox-k8s-clusterip-svc.yaml`

NodePort:

`kubectl create -f sandbox-k8s-nodeport-svc.yaml`

Load Balancer:

`kubectl create -f sandbox-k8s-loadbalancer-svc.yaml`

Ingress:

`kubectl create -f sandbox-k8s-ingress-svc.yaml`

Ingress (with TLS):

`kubectl create -f sandbox-k8s-ingress-tls-svc.yaml`

#### Get services
`kubectl get svc`

#### Call ClusterIP service from within a pod
`kubectl exec {pod name} -- curl -s http://{service IP}`

`kubectl exec {pod name} -- curl -s http://sandbox-k8s-clusterip-svc.default.svc.cluster.local`

`kubectl exec {pod name} -- curl -s http://sandbox-k8s-clusterip-svc.default`

`kubectl exec {pod name} -- curl -s http://sandbox-k8s-clusterip-svc-svc`

#### Call NodePort service from browser via MiniKube

`minikube service sandbox-k8s-nodeport-svc`

#### Call LoadBalancer service from browser via MiniKube

`minikube service sandbox-k8s-loadbalancer-svc`

#### Call Ingress service
No TLS:

`curl http://sandbox-k8s.example.com`

TLS:

`curl -k -v https://sandbox-k8s.example.com`

_Notes:_ 
* _Must update `/etc/hosts` to include service IP address mapping for `sandbox-k8s.example.com` from `kubectl get ingresses`_ 
* _Must enable Ingress in Minikube via the following command: `minikube addons enable ingress`_
* _For TLS, must create private key and self-signed certification (see below)_

#### Create private key and self-signed certificate

```
openssl genrsa -out tls.key 2048
openssl req -new -x509 -key tls.key -out tls.cert -days 360 -subj /CN=sandbox-k8s.example.com
kubectl create secret tls tls-secret --cert=tls.cert --key=tls.key
```

### Volumes

#### Create pod
`kubectl create -f sandbox-k8s-volume-pod.yaml`

#### Connect to containers to verify write/read of shared volume
```
kubectl exec sandbox-k8s-volume-pod -c sandbox-k8s-volume-container-1 -i -t -- /bin/sh
echo test1 > demo1/textfile1
exit
kubectl exec sandbox-k8s-volume-pod -c sandbox-k8s-volume-container-2 -i -t -- /bin/sh
cat demo2/textfile1
```


### Future topics

* Jobs
* CronJob
* Volumes
    * gitRepo
    * hostPath
    * Persistent Volumes & Claims
* Sidecar containers
