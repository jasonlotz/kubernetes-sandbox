# sandbox-kubernetes

## Docker Commands

### Build the container image
`docker build -t sandbox-k8s-app .`

### Run the image
`docker run --name sandbox-k8s-app-container -p 8080:8080 -d sandbox-k8s-app`

### Test the app
`curl localhost:8080`

### Connect to running container
`docker exec -it sandbox-k8s-app-container bash`

### Access logs from container
`docker logs {container id}`

### Stop the container
`docker stop sandbox-k8s-app-container`

### Remove the container
`docker rm sandbox-k8s-app-container`

### Login to Docker Hub
`docker login`

## Tag image
`docker tag sandbox-k8s-app jlotz/sandbox-k8s-app`

(Replace "jlotz" with your Docker Hub ID)

### Push to Docker Hub
`docker push jlotz/sandbox-k8s-app`

## Run image from a different machine
`docker run -p 8080:8080 -d jlotz/sandbox-k8s-app`

## Kubernetes Commands

These instructions assume that you have a working installation of `minikube` running locally and the `kubectl` tools installed. 

### Start/stop cluster
`minikube start`
`minikube stop`

### Open the minikube dashboard
`minikube dashboard`

### Verify cluster is working
`kubectl cluster-info`

### Deploy pod from YAML
`kubectl create -f sandbox-k8s-manual.yaml`

Specifying a namespace:

`kubectl create -f sandbox-k8s-manual.yaml -n custom-namespace`

### List pods using labels
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

### Describe the pod
`kubectl describe pod sandbox-k8s-manual`

### Get the full YAML or JSON for the pod
`kubectl get po sandbox-k8s-manual -o yaml`
`kubectl get po sandbox-k8s-manual -o json`

### Access logs from pod
Single container:
`kubectl logs sandbox-k8s-manual`

Multiple containers:
`kubectl logs sandbox-k8s-manual sandbox-k8s-app`

Previous iteration (after a failure/restart):

`kubectl logs sandbox-k8s-manual --previous`

### Setup port forwarding
`kubectl port-forward sandbox-k8s-manual 8888:8080`

### Test app
`curl localhost:8888`

Or, open web browser to http://localhost:8888

### Updating labels
`kubectl label po sandbox-k8s-manual env=test --overwrite`

### Adding annotations
`kubectl annotate pod sandbox-k8s-manual mycompany.com/someannotation="foo bar"`

### Get namespaces
`kubectl get ns`

### Get pods in a namespace
`kubectl get po -n kube-system`

### Create a namespace
From a YAML file:

`kubectl create -f custom-namespace.yaml`

From the command line:

`kubectl create namespace custom-namespace`

### Switch to a namespace
`kubectl config set-context $(kubectl config current-context) --namespace custom-namespace`

### Delete the pod
`kubectl delete po sandbox-k8s-app`

You can also delete more than one pod by specifying multiple, space-separated names:

`kubectl delete po pod1 pod2`

Using label selectors:

`kubectl delete po -l creation_method=manual`

All pods in a namespace:

`kubectl delete ns custom-namespace`

All pods in the current namespace:

`kubectl delete po --all`

All resources in the current namespace:

`kubectl delete all --all`

### Create replication controller
`kubectl create -f kubia-rc.yaml`

### Get replications controllers
`kubectl get rc`

### Describe replications controller
`kubectl describe rc sandbox-k8s-rc`

### Delete replication controller
`kubectl delete rs sandbox-k8s-rc`

### Create replication set
`kubectl create -f kubia-rs.yaml`

### Get replications sets
`kubectl get rs`

### Describe replications sets
`kubectl describe rc sandbox-k8s-rs`

### Delete replication sets
`kubectl delete rs sandbox-k8s-rs`





## Future topics

* Jobs
* CronJob