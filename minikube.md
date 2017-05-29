# Testing locally with MiniKube

Original article: [Getting Started with Kubernetes via Minikube](https://medium.com/@claudiopro/getting-started-with-kubernetes-via-minikube-ada8c7a29620)

## Prereqs

Check VT-x/AMD-v is supported

    cat /proc/cpuinfo | grep 'vmx\|svm'

Install VirtualBox

Install latest minikube from https://github.com/kubernetes/minikube/releases

    curl -Lo minikube \
    https://storage.googleapis.com/minikube/releases/v0.19.0/minikube-linux-amd64 \
    && chmod +x minikube && sudo mv minikube /usr/local/bin/

Install kubectl from https://github.com/kubernetes/kubernetes/releases

    curl -Lo kubectl \
    https://storage.googleapis.com/kubernetes-release/release/v1.6.4/bin/linux/amd64/kubectl \
    && chmod +x kubectl && sudo mv kubectl /usr/local/bin/

## smoke test minikube

    minikube start

    kubectl get pods --all-namespaces

    kubectl get nodes

## install hello-minikube pod

    minikube start

    kubectl run hello-minikube --image=gcr.io/google_containers

    kubectl get pods

    kubectl get deployments

    kubectl expose deployment hello-minikube --type=NodePort

    kubectl get services

Get external IP

    curl $(minikube service hello-minikube --url)

## Clean Up

    kubectl delete service,deployment hello-minikube

## Build and install a node service with Docker

    mkdir hello-node && cd hello-node && touch Dockerfile server.js

    vi server.js

```javascript
var http = require('http');
var handleRequest = function(request, response) {
    response.writeHead(200);
      response.end('Hello World!');
};
var helloServer = http.createServer(handleRequest);
helloServer.listen(8080);
```

    vi Dockerfile

```
FROM node:4.4
EXPOSE 8080
COPY server.js .
CMD node server.js
```


    eval $(minikube docker-env)         # Set the docker env

    docker build -t hello-node:v1 .

    docker images

Deploy hello-node pod

    kubectl run hello-node --image=hello-node:v1 --port=8080

Expose the deployment to an external IP

    kubectl expose deployment hello-node --type=NodePort

    kubectl get services

Test Hello World

    curl $(minikube service hello-node --url)

## Clean Up

    kubectl delete service,deployment hello-node

    minikube stop
