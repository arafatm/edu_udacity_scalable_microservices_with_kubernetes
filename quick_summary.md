# Udacity: Scalable Microservices with Kubernetes

## Introduction to Microservices

##### Configure Your Cloud Shell Environment

    gcloud compute zones list

    gcloud config set compute/zone europe-west1-d

#### Download Go:

    wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -xzf go1.6.2.linux-amd64.tar.gz
    echo "export GOPATH=~/go" >> ~/.bashrc
    source ~/.bashrc

    mkdir -p $GOPATH/src/github.com/udacity
    cd $GOPATH/src/github.com/udacity
    git clone https://github.com/udacity/ud615

##### On shell 1 - build the app:

    cd $GOPATH/src/github.com/udacity/ud615/app
    mkdir bin

    go build -o ./bin/monolith ./monolith

    $ go get -u

##### On shell 1 - run the monolith server:

    sudo ./bin/monolith -http :10080

##### On shell 2 - test the app:

    curl http://127.0.0.1:10080

    curl http://127.0.0.1:10080/secure

##### On shell 2 - authenticate (password is password):

    curl http://127.0.0.1:10080/login -u user

##### On shell 2 - login and assign the value of the JWT to a variable

    TOKEN=$(curl http://127.0.0.1:10080/login -u user | jq -r '.token')

    echo $TOKEN

##### On shell 2 - access the secure endpoint using the JWT:

    curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure

##### On shell 2 - check out dependencies

    ls vendor
    cat vendor/vendor.json

### Refactor to MSA

    go build -o ./bin/hello ./hello

    sudo ./bin/hello -http 0.0.0.0:10082

    go build -o ./bin/auth ./auth

    sudo ./bin/auth -http :10090 -health :10091

    TOKEN=$(curl 127.0.0.1:10090/login -u user | jq -r '.token')

    curl -H "Authorization:  Bearer $TOKEN" http://127.0.0.1:10082/secure

### JWT: JSON Web Tokens

[jwt.io](https://jwt.io)

### Installing apps with native OS tools

    gcloud compute zones list

    gcloud config set compute/zone <zone>

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

## Building the Containers with Docker

### Installing Images with Docker

    sudo apt-get install docker.io

    sudo docker images

    sudo docker pull nginx:1.10.0

    sudo docker images

    sudo dpkg -l | grep nginx

    sudo apt-get update
    sudo apt-get install nginx

### Running Images with Docker

    sudo docker run -d nginx:1.10.0

    sudo docker ps

    sudo docker run -d nginx:1.9.3

    sudo docker run -d nginx:1.10.0

    sudo docker ps

    sudo ps aux | grep nginx

### Talking to Docker instances

    sudo docker ps

    sudo docker ps -aq

    sudo docker inspect f86cf066c304 # or sudo docker inspect sharp_bartik

    CN="sharp_bartik"

    CIP=$(sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' $CN)

    curl  http://$CIP

    sudo docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(sudo docker ps -aq)

    sudo docker stop <cid> # or sudo docker stop $(sudo docker ps -aq)

    sudo docker ps

    sudo docker rm <cid> # or sudo docker rm $(sudo docker ps -aq)

### Create An Image

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

### Push Images

    sudo docker images

    docker tag -h

    sudo docker tag monolith:1.0.0 <your username>/monolith:1.0.0

    sudo docker tag monolith:1.0.0 udacity/example-monolith:1.0.0

    sudo docker login

    sudo docker push udacity/example-monolith:1.0.0

## Kubernetes

### Setting up Kubernetes for this course

    cd $GOPATH/src/github/com/udacity/ud615/kubernetes

    gcloud container clusters create k0

### Kubernetes Intro Demo

    kubectl run nginx --image=nginx:1.10.0

    kubectl get pods

    kubectl expose deployment nginx --port 80 --type LoadBalancer

    kubectl get services

### Pods Intro


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

### Creating Pods

    cat pods/monolith.yaml

    kubectl create -f pods/monolith.yaml

    kubectl get pods

    kubectl describe pods monolith

### Interacting with Pods

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

### MHC (Moniting and Health Checks) Overview

    kubectl describe pods healthy-monolith | grep Readiness

    kubectl describe pods healthy-monolith | grep Liveness

### Creating Secrets

    ls tls

    kubectl create secret generic tls-certs --from-file=tls/

    kubectl describe secrets tls-certs

    kubectl create configmap nginx-proxy-conf --from-file=nginx/proxy.conf

    kubectl describe configmap nginx-proxy-conf

#### TLS and SSL: Accessing a Secure HTTPS Endpoint

    cat pods/secure-monolith.yaml

    kubectl create -f pods/secure-monolith.yaml

    kubectl get pods secure-monolith

    kubectl port-forward secure-monolith 10443:443

    curl --cacert tls/ca.pem https://127.0.0.1:10443

    kubectl logs -c nginx secure-monolith

### Services Overview

    cat services/monolith.yaml

    kubectl create -f services/monolith.yaml

    gcloud compute firewall-rules create allow-monolith-nodeport --allow=tcp:31000

    gcloud compute instances list

    curl -k https://<external ip>:31000

### Adding Labels to Pods

    kubectl get pods -l "app=monolith"

    kubectl get pods -l "app=monolith,secure=enabled"

    kubectl label pods secure-monolith "secure=enabled"

    kubectl describe pods secure-monolith |grep Labels

    kubectl describe pods secure-monolith |grep Endpoints

    curl -k https://<external ip>:31000

## Deploying Microservices

### Creating Deployments

    cat deployments/auth.yaml

    kubectl create -f deployments/auth.yaml

    kubectl describe deployments auth

    kubectl create -f services/auth.yaml

    kubectl create -f deployments/hello.yaml

    kubectl create -f services/hello.yaml

    kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf

    kubectl create -f deployments/frontend.yaml

    kubectl create -f services/frontend.yaml

    kubectl get services frontend

    curl -k https://<external ip>

### Scaling Deployments

    kubectl get replicasets

    kubectl get pods -l "app=hello,track=stable"

    vim deployments/hello.yaml

    kubectl apply -f deployments/hello.yaml

    kubectl get pods

    kubectl describe deployment hello

### Rolling Updates

    vi deployments/auth.yaml

    kubectl apply -f deployments/auth.yaml

    kubectl describe deployments auth

    kubectl get pods

    kubectl describe pods auth-<xxx>

## Resources

#### People

- Kelsey Hightower [@kelseyhightower](https://twitter.com/kelseyhightower)
- Carter Morgan [@\_askcarter](https://twitter.com/_askcarter)
- Adrian Cockcroft [@adrianco](https://twitter.com/adrianco)
- Gundega Dekena [@pytonc](https://twitter.com/pytonc)

#### Books

- [Kubernetes: Up and Running, Kelsey
  Hightower](http://shop.oreilly.com/product/0636920043874.do) The definitive
  book on Kubernetes. This has been a great resource while making this course.
- [Building Microservices: Defining Fine-Grained
  Systems](http://shop.oreilly.com/product/0636920033158.do) This is the book
  Kelsey reads before giving talks about microservices. Its that good.

#### Articles

- Martin Fowler on the
  [Pros](http://martinfowler.com/articles/microservices.html) and [Cons of
  Microservices](http://martinfowler.com/articles/microservice-trade-offs.html)
- [12-Fractured
  Apps](https://medium.com/@kelseyhightower/12-fractured-apps-1080c73d481c) -
  One of Carters favorites articles where Kelsey breaks down problems with many
  modern apps and how 12-factor app methodology solves those technical woes.
- Tim OReilly, ["Open Data: Small Pieces Loosely
  Joined"](http://radar.oreilly.com/2006/09/open-data-small-pieces-loosely.html)
  For the history nerds: Quite possibly the first article about Microservices
  Architecture (before it even had a name).

#### Videos

- Adrian Cockroft ["The Evolution of
  Microservices"](http://www.ustream.tv/recorded/86151804)
- Adrian Cockroft ["The State of the Art in
  Microservices"](https://www.youtube.com/watch?v=pwpxq9-uw_0) (docker
  specific)
- Martin Fowler ["Microservices"](https://www.youtube.com/watch?v=wgdBVIX9ifA)
  at goto
- Craig McLuckie ["The Next Chapter in Native Cloud
  Computing"](https://www.youtube.com/watch?v=mPhjFYXoAD0) on cloud-native
  computing as being: container-packaged, dynamically-scheduled, and
  microservices-oriented

#### Tools

- [https://golang.org/](https://golang.org/)

- [https://cloud.google.com/shell/docs/](https://cloud.google.com/shell/docs/)

- [https://www.docker.com/](https://www.docker.com/)

- [http://kubernetes.io/](http://kubernetes.io/)

- [https://cloud.google.com/container-engine/](https://cloud.google.com/container-engine/)
