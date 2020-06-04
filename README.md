# Capstone Project - Cloud Devops Engineer Nanodegree Program

Autor: Antonio J. Pérez Montilla

## Description

Deploy a html application uses docker and kubernetes over AWS EKS. For the project, I used Jenkins as Pipeline to implement Continuous Integration and Continuous Deployment. I uses blue/green strategy to deploy the project

## Dependencies

Previous to deploy it's necesary to install:

* Docker
* Python
* Pip 
* AWS Cli (last version include kubectl)
* Eksctl
* Jenkins
* Ocen Blue Plugin for Jenkins
* Pipeline AWS for Jenkins

## Steps of project (Pipeline steps)

1. App Code is checked against a linter
2. Build Docker image
3. Upload Docker image to Docker hub
4. Create AWS Kubernetes Cluster (I used the ekstcl tool to deploy the cluster)
5. Set up the context of kubectl to work with the cluster
6. Create the green controller with the docker image and set up service in Kubernetes
7. Create the blue controller with the docker image and set up service in Kubernetes
8. Remove blue deployment from AWS Loadbalancer
9. Remove green deployment from AWS Loadbalancer

## Files description

* Jenkinsfile :  Pipeline file
* Dockerfile: file to build the docker image
* green-deploy.yml: Set up a replication controller green pod and the green service
* blue-deploy.yml: Set up a replication controller blue pod and the blue service
