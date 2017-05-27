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


## Building the Containers with Docker

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

