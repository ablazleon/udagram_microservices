# udagram_microservices

CI/CD deployment of Udagram refactored as microservices. 

***Credits***
Udacity Cloud Developer Nanodegree Program

## Intro

## What I learnt

## What I did

-----------

## Intro

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into two parts:
1. Frontend - Angular web application built with Ionic Framework
2. Backend RESTful API - Node-Express application

## Getting Started
> _tip_: it's recommended that you start with getting the backend API running since the frontend web application depends on the API.

### Prerequisite
1. The depends on the Node Package Manager (NPM). You will need to download and install Node from [https://nodejs.com/en/download](https://nodejs.org/en/download/). This will allow you to be able to run `npm` commands.
2. Environment variables will need to be set. These environment variables include database connection details that should not be hard-coded into the application code.
#### Environment Script
A file named `set_env.sh` has been prepared as an optional tool to help you configure these variables on your local development environment.
 
We do _not_ want your credentials to be stored in git. After pulling this `starter` project, run the following command to tell git to stop tracking the script in git but keep it stored locally. This way, you can use the script for your convenience and reduce risk of exposing your credentials.
`git rm --cached set_env.sh`

Afterwards, we can prevent the file from being included in your solution by adding the file to our `.gitignore` file.

### Database
Create a PostgreSQL database either locally or on AWS RDS. Set the config values for environment variables prefixed with `POSTGRES_` in `set_env.sh`.

### S3
Create an AWS S3 bucket. Set the config values for environment variables prefixed with `AWS_` in `set_env.sh`.

### Backend API
* To download all the package dependencies, run the command from the directory `udagram-api/`:
    ```bash
    npm install .
    ```
* To run the application locally, run:
    ```bash
    npm run dev
    ```
* You can visit `http://localhost:8080/api/v0/feed` in your web browser to verify that the application is running. You should see a JSON payload. Feel free to play around with Postman to test the API's.

### Frontend App
* To download all the package dependencies, run the command from the directory `udagram-frontend/`:
    ```bash
    npm install .
    ```
* Install Ionic Framework's Command Line tools for us to build and run the application:
    ```bash
    npm install -g ionic
    ```
* Prepare your application by compiling them into static files.
    ```bash
    ionic build
    ```
* Run the application locally using files created from the `ionic build` command.
    ```bash
    ionic serve
    ```
* You can visit `http://localhost:8100` in your web browser to verify that the application is running. You should see a web interface.

## Tips
1. Take a look at `udagram-api` -- does it look like we can divide it into two modules to be deployed as separate microservices?
2. The `.dockerignore` file is included for your convenience to not copy `node_modules`. Copying this over into a Docker container might cause issues if your local environment is a different operating system than the Docker image (ex. Windows or MacOS vs. Linux).
3. It's useful to "lint" your code so that changes in the codebase adhere to a coding standard. This helps alleviate issues when developers use different styles of coding. `eslint` has been set up for TypeScript in the codebase for you. To lint your code, run the following:
    ```bash
    npx eslint --ext .js,.ts src/
    ```
    To have your code fixed automatically, run
    ```bash
    npx eslint --ext .js,.ts src/ --fix
    ```
4. Over time, our code will become outdated and inevitably run into security vulnerabilities. To address them, you can run:
    ```bash
    npm audit fix
    ```
5. In `set_env.sh`, environment variables are set with `export $VAR=value`. Setting it this way is not permanent; every time you open a new terminal, you will have to run `set_env.sh` to reconfigure your environment variables. To verify if your environment variable is set, you can check the variable with a command like `echo $POSTGRES_USERNAME`.

## What I learnt

The motivation of this project is to understand how to turn a "monolith" software into services. To do so it is taken the rest api and approach it as two separate projects running in two different docker instances, and fianlly as two containers in a kubernetes cluster.

First, I started approaching this CI/CD pipeline as follows.

![cicd1](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/cicd1.png)

Then, asking to some friends they told they are now using Jenkins X beacuse it allows converging every step in git: so I propose to project with Jenkins X as I thought I will be appreciated by mentors. But then I found, there were some problems related envrionemtn varialbes in jenkins X, so I first decided trying wiht kubernetes deployment alone.

![cicd2](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/cicd2.png)

### Steps

1 Using jenkins X => a problem in an environment: services seems unaccesible and also the frotend=> so it is accessed with k8s alone

![GKE-logs](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/GKE-logs.png)

![GKE-logs-env](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/GKE-logs-env.png)

![Frontend-gke-problem](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/Frontend-gke-problem.png)

#### 1.1 Set up cluster
#### 1.2 Set up the service
#### 1.3 Develop the service

#### 2. With k8s alone

#### 1 Set up cluster

From https://jenkins-x.io/docs/install-setup/install-binarls, jx is installed.

Then, I created an eks cluster, but as the costs grows so quickly I decided to choose instead gke, to the tests and deploy the final in eks.

    ```bash
    jx create cluster gke --cluster-name mycluster --skip-installation --skip-login=true
    ```

    ```bash
    jx create cluster eks --cluster-name mycluster --skip-installation 
    ```

Then it is copied a jx-requirments.yml and modified it accordingly.

    ```bash
    jx boot 
    ```

To check it works, it is created a quickstart. Then it is checked that the reverse proxy is a service.

    ```bash
    kubectl get svc
    ```

#### 2 Set up the service

First, both services are deployed independently.

Then, both the v0 microservices are imported and took to production.

    ```bash
    jx import --url https://github.com/ablazleon/udagram-api-users.git
    jx import --url https://github.com/ablazleon/udagram-api-feed.git
    jx import --url https://github.com/ablazleon/udagram-frontend.git
    jx get app
    ```
It is config the env values in the dev environmanet in a values.yml file

In this photo it is shown how both microservices work.


    ```bash
    jx promote ---env production
    ```

#### 3 Develop the service

From there it is refactored the microservices in dev enviroment, importing the microservice feed and removing the users functionality from the the other microservice.

Here it is icnlude that these works.

THen it is included a .travis to run the steps out of the cluster

---------------------

#### 2. With k8s alone

To do so it is created some kubernetes arquitecture with a reverse proxy and services and a config map

    ```bash
    eksctl create cluster \
     --name c2 \
     --version 1.16 \
     --without-nodegroup
    ```
    
    ```bash
    eksctl create nodegroup \
      --cluster c2 \
      --region us-west-2 \
      --name my-mng \
      --node-type m5.large \
      --nodes 3 \
      --nodes-min 2 \
      --nodes-max 4 
    ```

# What I did: rubric

Containers and Microservices

- [x] ***Divide an application into microservices***: /feed and /user backends are separated into independent projects.

It is shown how the three services are deployed succesfully.

![Local](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/Local.png)

- [x] ***Build and run a container image using Docker***: Project includes Dockerfiles to successfully create Docker images for /feed, /user backends, project frontend, and reverse proxy. Screenshot of DockerHub shows the images.


Independent Releases and Deployments

- [x] ***Use Travis to build a CI/CD pipeline***: Project includes a .travis.yml file. Screenshot of the Travis CI interface shows a successful build and deploy job.

This image shows that in the travis dashboard the job is done succesfully.

![Travis](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/Travis.png)

Service Orchestration with Kubernetes

- [x] ***Deploy microservices using a Kubernetes cluster on AWS***: 

THe deployment followed this configuration:

[![](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggTFJcbiAgQVtDbGllbnRdIC0tPiBCW0xvYWQgQmFsYW5jZXIgLSBObmdpeF1cbiAgQiAtLT4gQ1tmcm9udGVuZF1cbiAgQiAtLT4gRFt1c2Vycy1hcGldXG4gIEIgLS0-IEVbZmVlZC1hcGldXG4iLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9fQ)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggTFJcbiAgQVtDbGllbnRdIC0tPiBCW0xvYWQgQmFsYW5jZXIgLSBObmdpeF1cbiAgQiAtLT4gQ1tmcm9udGVuZF1cbiAgQiAtLT4gRFt1c2Vycy1hcGldXG4gIEIgLS0-IEVbZmVlZC1hcGldXG4iLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9fQ)

- A screenshots of kubectl commands show the Frontend and API projects deployed in Kubernetes.

- The output of kubectl get pods indicates that the pods are running successfully with the STATUS value Running.

- The output of kubectl describe services does not expose any sensitive strings such as database passwords.

- [x] ***Use a reverse proxy to direct requests to the appropriate backend***: Screenshot of Kubernetes services shows a reverse proxy

It is showed the serverse proxy.

![Reverse proxy](https://github.com/ablazleon/udagram_microservices/blob/master/screenshots/nginex-reverserpoxy.png)

- [x] ***Configure scaling and self-healing for each service***: 

- Kubernetes services are replicated. At least one of the Kubernetes services has replicas: defined with a value greater than 1 in its deployment.yml file.

- Screenshot of Kubernetes cluster of command kubectl describe hpa has autoscaling configured with CPU metrics.

Debugging, Monitoring, and Logging

- [x] ***Use logs to capture metrics for debugging a microservices deployment***: Screenshot of one of the backend API pod logs indicates user activity that is logged when an API call is made.
