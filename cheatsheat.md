# Cheat sheet

## cat yaml configs

    cat deployments/auth.yaml

    cat Dockerfile

    cat pods/monolith.yaml

    cat pods/secure-monolith.yaml

    cat services/monolith.yaml

    cat vendor/vendor.json

## Paths and ENV vars

    cd $GOPATH/src/github.com/udacity

    cd $GOPATH/src/github.com/udacity/ud615/app

    cd $GOPATH/src/github/com/udacity/ud615/kubernetes

    cd ud615/app/monolith

    CID=$(sudo docker run -d monolith:1.0.0)

    CIP=$(sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' ${CID})

    CIP=$(sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' $CN)

    CN="sharp_bartik"

    TOKEN=$(curl 127.0.0.1:10090/login -u user | jq -r '.token')

    TOKEN=$(curl http://127.0.0.1:10080/login -u user | jq -r '.token')

    curl --cacert tls/ca.pem https://127.0.0.1:10443

    curl $CIP

    curl -H "Authorization: Bearer <token>" http://127.0.0.1:10080/secure

    curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure

    curl -H "Authorization:  Bearer $TOKEN" http://127.0.0.1:10082/secure

    curl http://127.0.0.1

    curl http://127.0.0.1:10080

    curl http://127.0.0.1:10080/login -u user

    curl http://127.0.0.1:10080/secure

    curl http://$CIP

    curl -k https://<external ip>

    curl -k https://<external ip>:31000

    curl -k https://<external ip>:31000

    curl <the container IP>

    curl -u user http://127.0.0.1:10080/login

    echo "export GOPATH=~/go" >> ~/.bashrc

    echo $TOKEN

    export GOPATH=~/go

    export PATH=$PATH:/usr/local/go/bin

## gcloud

    gcloud compute firewall-rules create allow-monolith-nodeport --allow=tcp:31000

    gcloud compute instances create ubuntu --image-project ubuntu-os-cloud --image ubuntu-1604-xenial-v20160420c

    gcloud compute instances list

    gcloud compute ssh ubuntu

    gcloud compute zones list

    gcloud config set compute/zone europe-west1-d

    gcloud container clusters create k0

    git clone https://github.com/udacity/ud615

    git clone https://github.com/udacity/ud615.git

## Go

    wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz

    go build -o ./bin/auth ./auth

    go build -o ./bin/hello ./hello

    go build -o ./bin/monolith ./monolith

    go build --tags netgo --ldflags '-extldflags "-lm -lstdc++ -static"'

    $ go get -u 

    go get -u

## kubctl apply

    kubectl apply -f deployments/auth.yaml

    kubectl apply -f deployments/hello.yaml

## kubctl create configmap

    kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf

    kubectl create configmap nginx-proxy-conf --from-file=nginx/proxy.conf

## kubectl create -f

    kubectl create -f deployments/auth.yaml

    kubectl create -f deployments/frontend.yaml

    kubectl create -f deployments/hello.yaml

    kubectl create -f pods/monolith.yaml

    kubectl create -f pods/secure-monolith.yaml

    kubectl create -f services/auth.yaml

    kubectl create -f services/frontend.yaml

    kubectl create -f services/hello.yaml

    kubectl create -f services/monolith.yaml

## kubectl create secret

    kubectl create secret generic tls-certs --from-file=tls/

## kubectl describe

    kubectl describe configmap nginx-proxy-conf

    kubectl describe deployment hello

    kubectl describe deployments auth

    kubectl describe deployments auth

    kubectl describe pods auth-<xxx>

    kubectl describe pods healthy-monolith | grep Liveness

    kubectl describe pods healthy-monolith | grep Readiness

    kubectl describe pods monolith

    kubectl describe pods secure-monolith |grep Endpoints

    kubectl describe pods secure-monolith |grep Labels

    kubectl describe secrets tls-certs

## kubectl exec

    kubectl exec monolith --stdin --tty -c monolith /bin/sh

## kubectl expose

    kubectl expose deployment nginx --port 80 --type LoadBalancer

## kubectl get

    kubectl get pods

    kubectl get pods -l "app=hello,track=stable"

    kubectl get pods -l "app=monolith"

    kubectl get pods -l "app=monolith,secure=enabled"

    kubectl get pods secure-monolith

    kubectl get replicasets

    kubectl get services

    kubectl get services frontend

## kubectl label

    kubectl label pods secure-monolith "secure=enabled"

## kubectl logs

    kubectl logs -c nginx secure-monolith

    kubectl logs -f monolith

## kubectl port-forward

    kubectl port-forward monolith 10080:80

    kubectl port-forward secure-monolith 10443:443

## kubectl run

    kubectl run nginx --image=nginx:1.10.0

## apt-get

    sudo apt-get install docker.io

    sudo apt-get install nginx

    sudo apt-get install nginx

    sudo apt-get update

    sudo apt-get update

## docker

    sudo docker build -t monolith:1.0.0 .

    sudo docker images

    sudo docker images monolith:1.0.0

    sudo docker inspect <container name or cid>

    sudo docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(sudo docker ps -aq)

    sudo docker inspect sharp_bartik

    sudo docker login

    sudo docker ps -aq

    sudo docker pull nginx:1.10.0

    sudo docker push udacity/example-monolith:1.0.0

    sudo docker rm <cid> # or sudo docker rm $(sudo docker ps -aq)

    sudo docker run -d monolith:1.0.0

    sudo docker run -d nginx:1.10.0

    sudo docker run -d nginx:1.10.0

    sudo docker run -d nginx:1.9.3

    sudo docker stop <cid> # or sudo docker stop $(sudo docker ps -aq)

    sudo docker tag monolith:1.0.0 udacity/example-monolith:1.0.0

    sudo docker tag monolith:1.0.0 <your username>/monolith:1.0.0

