# 29.06.2019 Kubernetes Workshop

![maskots](./images/docker-maskots.jpg)

This is to hold the slides and all demo material for the meetup / workshop

## Requirements

- git [***source control system***]
- Docker Desktop [***container management tool***]

`Download the Docker Desktop application and install, if on Windows 10 switch to Linux context by clicking the Switch to Linux option from the whale in the tray.`

## Intro to Docker - Docker 101

![docker logo](./images/docker-horizontal-large.png)

### What is Docker?

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package.

### Dockerfile

`Navigate to the folder: 15.06.2019.Kubernetes.Workshop/demo/dockerfile`

A documented specification what how to create your application so that it can be executed.

**Example:**

    FROM node:11 AS build
    LABEL CLI-VERSION="6.0.0"
    RUN useradd --user-group --create-home --shell /bin/false app
    ENV HOME=/home/app
    WORKDIR $HOME
    RUN npm install -g @angular/cli@6.0.0 \
        && npm install @angular/compiler-cli@6.0.0 --save \
        && npm install yarn
    COPY awesome-sauce .
    RUN yarn install
    RUN ng build

    FROM nginx
    ADD default.conf /etc/nginx/conf.d/default.conf
    COPY --from=build /home/app/dist/awesome-sauce /usr/share/nginx/html
    EXPOSE 80

### Docker Image

A compiled specification of your software stored in a repository.

    docker build .

**Example: Docker will get the missing images**

![docker build](./images/dockerbuild.png)

**Example**

![docker get packages](./images/docker-build-get-packages.png)

After the build you can see the images created by running:

    docker images

**Example: docker images untagged**

![docker untagged images](./images/docker-images-untagged.png)

Name the image by tagging the image:

    docker build -t awesome-sauce:1 .

![tagged image](./images/docker-images-tagged.png)

### Run Image as a container

    docker run awesome-sauce:1

### Sharing Images

Share your image with others as sharing is caring.

    docker push awesome-sauce:1

### Docker Compose

![docker compose image](./images/docker-compose.png)

Docker comes with a tool to orchestrate your applications in a simple manner with a .yml file.

**Example:**

    version: '3'

    services:
        web:
        image: registry.gitlab.com/minutz/web:1
        ports:
            - '8090:80'

**_For more information check out [Docker-Compose Deploy Stack](https://docs.docker.com/engine/swarm/stack-deploy/)_**

## Kubernetes : 101

### What is Kubernetes

Kubernetes is an open-source container-orchestration system for automating application deployment, scaling, and management.

It was originally designed by Google, and is now maintained by the Cloud Native Computing Foundation.

![demo one deploy stack](./images/kubernets-logo.png)

### Get Kubernetes on your machine

Click on the Docker desktop application, then click preferences / settings select Kubernetes and check enable Kubernetes.

![demo one deploy stack](./images/enable-kube-docker.png)

## kubectl

kubectl controls the Kubernetes cluster manager.

### Check that all is working

kubectl get nodes

### Orchestration

Docker comes with swarm as the default orchestrator, you can set the orchestrator for all deployments or set it when deploying the stack.

- set DOCKER_STACK_ORCHESTRATOR=swarm
- set DOCKER_STACK_ORCHESTRATOR=kubernetes

Set the orchestrator when deploying:

    docker stack deploy --orchestrator kubernetes --compose-file demo-one-deploy.yml demo-one-stack

![demo one deploy stack](./images/demo-one-deploy-stack.png)

    kubectl get pods

![demo one deploy pods](./images/demo-one-deploy-pods.png)

### Remove Stack

    docker stack rm demo-one-stack

## Kubernetes top level

- **kubectl apply** : Manages applications through files defining Kubernetes resources. It creates and updates resources in a cluster.

**Examples**

    kubectl apply -f ./my-manifest.yaml           # create resource(s)
    kubectl apply -f ./my1.yaml -f ./my2.yaml     # create from multiple files
    kubectl apply -f ./dir                        # create resource(s) in all manifest files in dir
    kubectl apply -f https://git.io/vPieo         # create resource(s) from url
    kubectl create deployment nginx --image=nginx  # start a single instance of nginx
    kubectl explain pods,svc                       # get the documentation for pod and svc manifests

- **kubectl get services**: List all services in the namespace
- **kubectl get pods --all-namespaces**: List all pods in all namespaces
- **kubectl get deployment my-dep**: List a particular deployment
- **kubectl describe pods my-pod**: Describe commands with verbose output

**_For more check out [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)_**

## Kubernetes Dashboard

    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml

Now set the proxy so that you can navigate to the dashboard

    kubectl proxy

Copy and paste into a browser:

    http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

![demo one deploy pods](./images/dashboard-login-token.png)

### Get the token from kubctl

    $TOKEN=((kubectl -n kube-system describe secret default | Select-String "token:") -split " +")[1]

### Set the token to the cluster

    kubectl config set-credentials docker-for-desktop --token="${TOKEN}"

### Set the config file to the dashboard

- Click on Kubeconfig and select the “config” file under /users/<username>.kube\config

Once logged in you will see the dashboard.

![kubernetes dashboard](./images/kubernetes-dashboard.png)

### Nodes in your cluster

![kubernetes dashboard](./images/kub-nodes-dashboard.png)

### Roles in your cluster

![kubernetes dashboard](./images/roles-kube-dashboard.png)

To Stop the dashboard simply exit the proxy command from earlier.
