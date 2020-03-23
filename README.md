# Banking App Infrastructure

This repository is part of the microservice training for the Software
Engineering Session.

## Contents

This repository contains the Kubernetes configuration for shared infrastructure
used by the microservices. This includes:

- The [RabbitMQ](https://www.rabbitmq.com/) message broker
- The Ingress API routes

## Deploying

### 1 Update the github id in the Jenkinsfile

### 2 Update the hosts in ingress.yml to a unique domain name

### 3. Deploy

`kubectl -n NAMESPACE apply -f kubernetes/`

Note: There is an included [Jenkinsfile](./Jenkinsfile) which allows this step
to be done via a pipeline.