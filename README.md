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