Containerization : It is an form of OS Virtualization, so apps runs in isolated user spaces, shares same OS.

Why Containerization : To move from Monolithic architecture to Microservices architecture.

Example : Hotstar scales up and down accordingly based on traffic, microservices here.

Why Docker : It is containerization technology.

##### Kubernetes (K8s) 

Why Kubernetes : It is for managing containers.

It is an orchestration tool offers High Availability, Scalability, and Disaster recovery.

##### What Kubernetes provides :

Service discovery & load balancing - K8s balances container high traffic by redirecting to other containers.

Storage orchestration - Options of mounting local storage and public cloud providers.

Automated rollouts and rollbacks - K8s can create new containers for deployment, removing existing ones and adapt all their resources to the new container.

Automatic bin packing - K8s can fit containers onto your nodes, with best use of resources (CPU, RAM)

Self-healing - K8s restarts/replace/kills containers if not responded by user-defined health checks.

Secret & configuration management - K8s lets to store passwords, token, keys, without exposing it in stack config.

##### Kubernetes Terms

When you deploy Kubernetes, you get K8s cluster.

What K8s cluster consist of - Set of master nodes called Control plane.

What master nodes do - Manages worker machines, called worker nodes.

What is worker nodes - It host the pods that are the components of the application workload.

What is pods - It is the containers which runs an app (Docker Image).

##### Kubernetes Components

Pods - It is something on top of Docker, called containers.

Service - It makes sure every pod has a fixed IP address, so even DB pod dies, it maps the same IP for new DB pod created, also acts as load balancer means it sees which pods are active and according pass requests.

Ingress - For same host and different endpoints, Ingress takes care of mapping each endpoints to respective IP address, assume like DNS.

Deployment - It is not possible to deploy each pod, So Deployment helps to run a command and deploy everything for us, this is one way of deployment which is stateless.

Stateful set - Deployment needs some storage mechanism, where storage is in read and write mode, then we call it as Stateful set, this is other way of deployment which is Stateful.

Volumes - For persisting data.

Config map - Config file to share IP address, endpoints, DNS lookups..

Secrets - Object holds sensitive data such as password, token, key.

##### Kubernetes Architecture

What all it takes to make a worker node ? - Kubelet and Kubeproxy

What is Kubelet - It is the one going to launch the container, it starts the pod.

What is Kubeproxy - To have communication between nodes.

Then what master node do ? - It manages, schedules, monitor, reschedule/restart, adds the pod.

How ? - Using Kube-API Server, Scheduler, Controller manager - Node and Replication, Etcd.

API-Server - It is the frontend to the cluster, all external communications only via API-Server, and it talks to Kube-Scheduler and then it goes to Kubelet to send all the communications to worker nodes.

Kube-Scheduler - It receives request for API-Server, schedules activities to worker nodes based on events occurring on Etcd, Now it goes and instructs the Kubelet in the worker node.

Kube-Controller-Manager - It runs set of controllers for the running cluster, it detects cluster state changes, if pod died, it will send the request to the scheduler and scheduler talks to Kubelet.

Etcd - It is like DB stores key value pairs. 

Example : End user requests some URL --> it goes to API-Server-->it sends the info to Scheduler-->It talks to Etcd to get the state of nodes-->Etcd gets the info from Control Manager-->Control Manager is in touch with worker nodes... This way, finally it talks to Kubelet to start the pod.