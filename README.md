# Udacity: Scalable Microservices with Kubernetes

## Introduction to Microservices

### The Evolution of Applications w/ Adrian Cockroft

Microservices: Breaking software into smaller pieces in containers.
- allows faster deployment of containerized
- smaller deployments => localized bugs that doesn't block entire org
- allow teams to work at different paces

*Don't* coordinate dependencies. Allow independent deployment cycles

### Microservices

Microservices := architecture approach to make software
- modular
- easy to deploy
- scale independently

### Setup GCE and Enable Cloud Shell

<details>
<summary>In this section you will create a Google Compute Engine (GCE) account.
GCE will allow you to the create VMs, Networks, and Storage volumes required
for this workshop.</summary>

<p>

GCE also provides the [Cloud Shell](https://cloud.google.com/shell/docs)
computing environment that will be used complete the labs.

#### Create a GCE Account

A Google Cloud Platform account is required for this course. You can use an
existing GCP account or [sign up for a new
one](https://console.cloud.google.com/freetrial) with a valid Gmail account.

A credit card is required for Google Cloud Platform, but there is no
subscription cost, you pay only for resources you use.

#### Create a Project

A GCP project is required for this course. You can use an existing GCP project or [create a new one](https://support.google.com/cloud/answer/6251787).

Your project name maybe different from your project id.

#### Enable Compute Engine and Container Engine APIs

In order to create the cloud resources required by this workshop, you will need to enable the following APIs using the [Google API Console](https://developers.googleblog.com/2016/03/introducing-google-api-console.html):
*   **Compute Engine API**
*   **Container Engine API**

#### Enable and explore Cloud Shell

[Google Cloud Shell](https://cloud.google.com/shell/docs) provides you with
command-line access to computing resources hosted on Google Cloud Platform and
is available now in the Google Cloud Platform Console. Cloud Shell makes it
easy for you to **manage your Cloud Platform Console projects and resources
without having to install the Google Cloud SDK and other tools on your
system**. With Cloud Shell, the Cloud SDK gcloud command and other utilities
you need are always available when you need them.

##### Explore Google Cloud Shell

Visit the [Google Cloud Shell getting started
guide](https://cloud.google.com/shell/docs/quickstart) and work through the
exercises.

##### Configure Your Cloud Shell Environment

Create two Cloud Shell Sessions and run the following commands to avoid setting
the compute zone.

List available time zones:

    gcloud compute zones list

Set a time zone example:

    gcloud config set compute/zone europe-west1-d

#### Download Go:

**Note**: Cloud Shell comes with an installed Go, but it's not the most recent
version, so you should perform the steps below to install the latest Go and set
GOPATH.

    wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -xzf go1.6.2.linux-amd64.tar.gz
    echo "export GOPATH=~/go" >> ~/.bashrc
    source ~/.bashrc

Get the code:

    mkdir -p $GOPATH/src/github.com/udacity
    cd $GOPATH/src/github.com/udacity
    git clone https://github.com/udacity/ud615

</p></details>


### Build and Interact with Monolith

Sample app is running 3 services
- Monolith
- Hello
- Auth Service

Note: Monolith is self-contained. Check `/vendor`

<details>
<summary>
#### Commands to run
</summary>
<p>

##### On shell 1 - build the app:

Make sure you are in the app directory and build the app:

    cd $GOPATH/src/github.com/udacity/ud615/app
    mkdir bin

    go build -o ./bin/monolith ./monolith

Optional - if you run into errors building your go binaries, you probably need to install the dependencies first by running:

    $ go get -u 

##### On shell 1 - run the monolith server:

    sudo ./bin/monolith -http :10080

##### On shell 2 - test the app:

    curl http://127.0.0.1:10080

    curl http://127.0.0.1:10080/secure

##### On shell 2 - authenticate (password is password):

    curl http://127.0.0.1:10080/login -u user

It prints out the token.

You can copy and paste the long token in to the next command manually, but
copying long, wrapped lines in cloud shell is broken. To work around this, you
can either copy the JWT token in pieces, or - more easily - by assigning the
token to a shell variable as follows

##### On shell 2 - login and assign the value of the JWT to a variable

    TOKEN=$(curl http://127.0.0.1:10080/login -u user | jq -r '.token')

Check that it worked:

    echo $TOKEN

##### On shell 2 - access the secure endpoint using the JWT:

    curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure

##### On shell 2 - check out dependencies

    ls vendor 
    cat vendor/vendor.json

</p>
</details>

### 12 Facter

Best practice for building SaaS
- portable
- deployable: e.g. aws, gcp
- continually deployable
- scalable: to user demand

### Refactor to MSA

<details>
<summary>
Refactor to MSA
</summary>
<p>

Shell 1 - build and run the hello service

    go build -o ./bin/hello ./hello

    sudo ./bin/hello -http 0.0.0.0:10082

Shell 2 - build and run the auth service

    go build -o ./bin/auth ./auth

    sudo ./bin/auth -http :10090 -health :10091

Shell 3 - interact with the auth and hello microservices

    TOKEN=$(curl 127.0.0.1:10090/login -u user | jq -r '.token')

    curl -H "Authorization:  Bearer $TOKEN" http://127.0.0.1:10082/secure


</p></details>

### JWT: JSON Web Tokens

JWT := compact self contained method for transferring secure data as a JSON

Uses
- Authentication
- Information Exchange

To test a token paste it in [jwt.io](https://jwt.io)

How does it work?
- client sends auth request to server
- server generates token
- server returns token to client
- client submits request with token
- server verifies the token
- server returns payload


## Building the Containers with Docker

### Installing apps with native OS tools

<details>
<summary>
#### Commands to run
</summary>
<p>

Cloud shell - set compute/zone
- Google Cloud shell is an ephemeral instance and will reset if you don't use it for more than 30 minutes. That is why you might have to set some configuration values again

    gcloud compute zones list
    gcloud config set compute/zone <zone>

Cloud shell - launch a new VM instance

    gcloud compute instances create ubuntu \
    --image-project ubuntu-os-cloud \
    --image ubuntu-1604-xenial-v20160420c 

Cloud shell - log into the VM instance

    gcloud compute ssh ubuntu

VM instance - update packages and install nginx

    sudo apt-get update
    sudo apt-get install nginx
    nginx -v

VM instance - start nginx

    sudo systemctl start nginx

Check that it's running

    sudo systemctl status nginx

    curl http://127.0.0.1

</p></details>

### Container Overview

Solve for installing and running apps across multiple environments
- independent packages
- namespace isolation

### Installing Images with Docker

<details>
<summary>
Commands to run (on the VM Instance)
</summary>
<p>
```
sudo apt-get install docker.io

# Check Docker images

sudo docker images

# Pull nginx image

sudo docker pull nginx:1.10.0
sudo docker images

# Verify the versions match

sudo dpkg -l | grep nginx

# If your version of nginx from native package and Docker are different,
# you need to update the VM instance:

sudo apt-get update
sudo apt-get install nginx
```
</p>

</details>

### Running Images with Docker

<details>
<summary>
Commands to run on the VM Instance
</summary>
<p>
```
# Run the first instance

sudo docker run -d nginx:1.10.0

# Check if it's up

sudo docker ps

# Run a different version of nginx

sudo docker run -d nginx:1.9.3

# Run another version of nginx

sudo docker run -d nginx:1.10.0

# Check how many instances are running

sudo docker ps
sudo ps aux | grep nginx
```

What's with the container names?

If you don't specify a name, Docker gives a container a random name, such as
"stoic_williams," "sharp_bartik," "awesome_murdock," or "evil_hawking."
(Stephen Hawking got no love on this one.)

These are generated from a list of adjectives and names of famous scientists
and hackers. The combination of the names and adjectives is random, except for
one case. Want to see what the exception is? Check it out in the [Docker source
code!](https://github.com/moby/moby/blob/master/pkg/namesgenerator/names-generator.go)

</p></details>


### Talking to Docker instances
### Creating your own images overview
### Create An Image
### Create the Other Containers
### Public vs Private Registries
### Push Images
### Lesson 2 Outro

## Kubernetes

## Deploying Microservices

## Resources

#### People

- Kelsey Hightower [@kelseyhightower](https://twitter.com/kelseyhightower)
- Carter Morgan [@\_askcarter](https://twitter.com/_askcarter)
- Adrian Cockcroft [@adrianco](https://twitter.com/adrianco)
- Gundega Dekena [@pytonc](https://twitter.com/pytonc)

#### Books

- [Kubernetes: Up and Running, Kelsey Hightower](http://shop.oreilly.com/product/0636920043874.do) The definitive book on Kubernetes. This has been a great resource while making this course.
- [Building Microservices: Defining Fine-Grained Systems](http://shop.oreilly.com/product/0636920033158.do) This is the book Kelsey reads before giving talks about microservices. Its that good.

#### Articles

- Martin Fowler on the [Pros](http://martinfowler.com/articles/microservices.html) and [Cons of Microservices](http://martinfowler.com/articles/microservice-trade-offs.html)
- [12-Fractured Apps](https://medium.com/@kelseyhightower/12-fractured-apps-1080c73d481c) - One of Carters favorites articles where Kelsey breaks down problems with many modern apps and how 12-factor app methodology solves those technical woes.
- Tim OReilly, ["Open Data: Small Pieces Loosely Joined"](http://radar.oreilly.com/2006/09/open-data-small-pieces-loosely.html) For the history nerds: Quite possibly the first article about Microservices Architecture (before it even had a name).

#### Videos

- Adrian Cockroft ["The Evolution of Microservices"](http://www.ustream.tv/recorded/86151804)
- Adrian Cockroft ["The State of the Art in Microservices"](https://www.youtube.com/watch?v=pwpxq9-uw_0) (docker specific)
- Martin Fowler ["Microservices"](https://www.youtube.com/watch?v=wgdBVIX9ifA) at goto
- Craig McLuckie ["The Next Chapter in Native Cloud Computing"](https://www.youtube.com/watch?v=mPhjFYXoAD0) on cloud-native computing as being: container-packaged, dynamically-scheduled, and microservices-oriented

#### Tools

The Go Programming Language
- Our app is written in Go. If youre not already using Go, you owe it to yourself to try it out.
- [https://golang.org/](https://golang.org/)

Google Cloud Shell
- A free temp VM preloaded with the tools need to manage our clusters.
- [https://cloud.google.com/shell/docs/](https://cloud.google.com/shell/docs/)

Docker
- We use Docker to package, distribute, and run our application.
- [https://www.docker.com/](https://www.docker.com/)

Kubernetes
- Once we have an application, we use Kubernetes to handle the heavy lifting of managing, deploying, and scaling our application.
- [http://kubernetes.io/](http://kubernetes.io/)

Google Container Engine (GKE)
- GKE is a hosted Kubernetes service
- [https://cloud.google.com/container-engine/](https://cloud.google.com/container-engine/)

