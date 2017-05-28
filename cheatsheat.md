# Cheat sheet

## gcloud zones

    gcloud compute zones list

    gcloud config set compute/zone europe-west1-d

## Install Go

    wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz

    sudo rm -rf /usr/local/go

    sudo tar -C /usr/local -xzf go1.6.2.linux-amd64.tar.gz

    echo "export GOPATH=~/go" >> ~/.bashrc

    source ~/.bashrc

## git clone udacity sample app

    mkdir -p $GOPATH/src/github.com/udacity

    cd $GOPATH/src/github.com/udacity

    git clone https://github.com/udacity/ud615

    cd $GOPATH/src/github.com/udacity/ud615/app

    mkdir bin

    go build -o ./bin/monolith ./monolith

    $ go get -u 

    sudo ./bin/monolith -http :10080

    curl http://127.0.0.1:10080

    curl http://127.0.0.1:10080/secure

    curl http://127.0.0.1:10080/login -u user

    TOKEN=$(curl http://127.0.0.1:10080/login -u user | jq -r '.token')

    echo $TOKEN

    curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure

    ls vendor 

    cat vendor/vendor.json

    go build -o ./bin/hello ./hello

    sudo ./bin/hello -http 0.0.0.0:10082

    go build -o ./bin/auth ./auth

    sudo ./bin/auth -http :10090 -health :10091

    TOKEN=$(curl 127.0.0.1:10090/login -u user | jq -r '.token')

    curl -H "Authorization:  Bearer $TOKEN" http://127.0.0.1:10082/secure

## create ubuntu instance

    gcloud compute instances create ubuntu \
    --image-project ubuntu-os-cloud \
    --image ubuntu-1604-xenial-v20160420c

    gcloud compute ssh ubuntu

    sudo apt-get update

    sudo apt-get install nginx

    nginx -v

    sudo systemctl start nginx

    sudo systemctl status nginx

    curl http://127.0.0.1

## Install docker

    sudo apt-get install docker.io

    sudo docker images

    sudo docker pull nginx:1.10.0

    sudo docker images

    sudo apt-get update

    sudo apt-get install nginx

    sudo docker run -d nginx:1.10.0

    sudo docker ps

    sudo docker run -d nginx:1.9.3

    sudo docker run -d nginx:1.10.0

    sudo docker ps

    sudo ps aux | grep nginx

    sudo docker ps

    sudo docker ps -aq

    sudo docker inspect f86cf066c304 # or
    sudo docker inspect sharp_bartik

    CN="sharp_bartik"

    CIP=$(sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' $CN)

    curl  http://$CIP

    sudo docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(sudo docker ps -aq)

    sudo docker stop <cid> # or
    sudo docker stop $(sudo docker ps -aq)

    sudo docker ps

    sudo docker rm <cid> # or
    sudo docker rm $(sudo docker ps -aq)

## Create a docker image

    wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz

    rm -rf /usr/local/bin/go

    sudo tar -C /usr/local -xzf go1.6.2.linux-amd64.tar.gz

    export PATH=$PATH:/usr/local/go/bin

    export GOPATH=~/go

    mkdir -p $GOPATH/src/github.com/udacity

    cd $GOPATH/src/github.com/udacity

    git clone https://github.com/udacity/ud615.git

    cd ud615/app/monolith

    go get -u

    go build --tags netgo --ldflags '-extldflags "-lm -lstdc++ -static"'

    cat Dockerfile

    sudo docker build -t monolith:1.0.0 .

    sudo docker images monolith:1.0.0

    sudo docker run -d monolith:1.0.0

    sudo docker inspect <container name or cid>

    CID=$(sudo docker run -d monolith:1.0.0)

    CIP=$(sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' ${CID})

    curl <the container IP>

    curl $CIP

## Publish images

    sudo docker images

    docker tag -h

    sudo docker tag monolith:1.0.0 <your username>/monolith:1.0.0

    sudo docker tag monolith:1.0.0 udacity/example-monolith:1.0.0

    sudo docker login

    sudo docker push udacity/example-monolith:1.0.0

## Kube

    cd $GOPATH/src/github/com/udacity/ud615/kubernetes

    gcloud container clusters create k0

    kubectl run nginx --image=nginx:1.10.0

    kubectl get pods

    kubectl expose deployment nginx --port 80 --type LoadBalancer

    kubectl get services

      --------------------- 172.10.1.100 --------
      |                                         |
      |              ------------               |
      |            / |   nginx  |               |
      |           |  ------------               |
      |           |                             |
      |           |  ------------               |
      |            \ | monolith |               |
      |              ------------               |
      |  POD                                    |
      |      _____       _______       _____    |
      |     (_____)     (_______)     (_____)   |
      ------| GCE | --- | iSCSI | --- | NFS | ---
            -------     ---------     -------

## Create a Pod
    cat pods/monolith.yaml

    kubectl create -f pods/monolith.yaml

    kubectl get pods

    kubectl describe pods monolith

## Interact with Pods

    kubectl port-forward monolith 10080:80

    curl http://127.0.0.1:10080

    curl http://127.0.0.1:10080/secure

    curl -u user http://127.0.0.1:10080/login

    curl -H "Authorization: Bearer <token>" http://127.0.0.1:10080/secure

    kubectl logs -f monolith

    curl http://127.0.0.1:10080

    Exit log watching (Ctrl-C)

    kubectl exec monolith --stdin --tty -c monolith /bin/sh

    ping -c 3 google.com

    exit

## MHC

    kubectl describe pods healthy-monolith | grep Readiness

    kubectl describe pods healthy-monolith | grep Liveness

## Secrets

    ls tls

    kubectl create secret generic tls-certs --from-file=tls/

    kubectl describe secrets tls-certs

    kubectl create configmap nginx-proxy-conf --from-file=nginx/proxy.conf

    kubectl describe configmap nginx-proxy-conf

    cat pods/secure-monolith.yaml

    kubectl create -f pods/secure-monolith.yaml

    kubectl get pods secure-monolith

    kubectl port-forward secure-monolith 10443:443

    curl --cacert tls/ca.pem https://127.0.0.1:10443

    kubectl logs -c nginx secure-monolith

## Labels

    cat services/monolith.yaml

    kubectl create -f services/monolith.yaml

    gcloud compute firewall-rules create allow-monolith-nodeport --allow=tcp:31000

    gcloud compute instances list

    curl -k https://<external ip>:31000

    kubectl get pods -l "app=monolith"

    kubectl get pods -l "app=monolith,secure=enabled"

    kubectl label pods secure-monolith "secure=enabled"

    kubectl describe pods secure-monolith |grep Labels

    kubectl describe pods secure-monolith |grep Endpoints

    curl -k https://<external ip>:31000

## Microservices
