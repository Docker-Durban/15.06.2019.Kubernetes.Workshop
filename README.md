# 29.06.2019 Kubernetes Workshop

This is to hold the slides and all demo material for the meetup / workshop

## Requirements

- Docker Desktop

`Download the Docker Desktop application and install, if on Windows 10 switch to Linux context by clicking the Switch to Linux option from the whale in the tray.`

## Intro to Docker - Docker 101

![docker logo](./images/docker-horizontal-large.png)

### What is Docker?

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package.

### Dockerfile

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
    COPY . .
    RUN yarn install
    RUN ng build --configuration=beta

    FROM nginx
    ADD default.conf /etc/nginx/conf.d/default.conf
    COPY --from=build /home/app/dist/minutz /usr/share/nginx/html
    EXPOSE 80

### Docker Image

A compiled specification of your software stored in a repository.

    docker build .

Name the image by tagging the image:

    docker build -t awesome-sauce:1 .

### Run Image as a container

    docker run awesome-sauce:1

### Sharing Images

Share your image with others as sharing is caring.

    docker push awesome-sauce:1

### Docker Compose

![docker compose image](./images/docker-compose.png)

Docker comes with a tool to orchestrate your applications in a simple manner with a .yml file.

    version: '3'

    services:
        web:
        image: registry.gitlab.com/minutz/web:1
        ports:
            - '8090:80'

## Kubernetes : 101

![demo one deploy stack](./images/kubernets-logo.png)

## Get Kubernetes on your machine

Click on the Docker desktop application, then click preferences / settings select Kubernetes and check enable Kubernetes.

![demo one deploy stack](./images/enable-kube-docker.png)

## Check that all is working

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
