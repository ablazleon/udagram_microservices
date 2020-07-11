# udagram_microservices

***Credits***
Udacity Cloud Developer Nanodegree Program

# What I did: rubric

Containers and Microservices

- [x] ***Divide an application into microservices***: /feed and /user backends are separated into independent projects.

- [x] ***Build and run a container image using Docker***: Project includes Dockerfiles to successfully create Docker images for /feed, /user backends, project frontend, and reverse proxy. Screenshot of DockerHub shows the images.

Independent Releases and Deployments

- [x] ***Use Travis to build a CI/CD pipeline***: Project includes a .travis.yml file. Screenshot of the Travis CI interface shows a successful build and deploy job.

Service Orchestration with Kubernetes

- [x] ***Deploy microservices using a Kubernetes cluster on AWS***: 

- A screenshots of kubectl commands show the Frontend and API projects deployed in Kubernetes.

- The output of kubectl get pods indicates that the pods are running successfully with the STATUS value Running.

- The output of kubectl describe services does not expose any sensitive strings such as database passwords.

- [x] ***Use a reverse proxy to direct requests to the appropriate backend***: Screenshot of Kubernetes services shows a reverse proxy

- [x] ***Configure scaling and self-healing for each service***: 

- Kubernetes services are replicated. At least one of the Kubernetes services has replicas: defined with a value greater than 1 in itsdeployment.yml file.

- Screenshot of Kubernetes cluster of command kubectl describe hpa has autoscaling configured with CPU metrics.

Debugging, Monitoring, and Logging

- [x] ***Use logs to capture metrics for debugging a microservices deployment***: Screenshot of one of the backend API pod logs indicates user activity that is logged when an API call is made.
