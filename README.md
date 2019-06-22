# 15.06.2019 Kubernetes Workshop

This is to hold the slides and all demo material for the meetup / workshop

## Intro to Docker - Docker 101

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
