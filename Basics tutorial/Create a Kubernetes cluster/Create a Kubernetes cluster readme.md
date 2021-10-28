https://kubernetes.io/docs/tutorials/kubernetes-basics/create-cluster/cluster-intro/

##### Using Minikube to Create a Cluster

Objectives

​	Learn what a Kubernetes cluster is.

​	Learn what Minikube is.

​	Start a Kubernetes cluster using an online terminal.

##### Kubernetes Clusters

Kubernetes coordinates a highly available cluster of computers that are connected to work as a single unit. The abstractions in Kubernetes allow you to deploy containerized applications to a cluster without tying them specifically to individual machines. To make use of this new model of deployment, applications need to be packaged in a way that decouples them from individual hosts: they need to be containerized. Containerized applications are more flexible and available than in past deployment models, where applications were installed directly onto specific machines as packages deeply integrated into the host. Kubernetes automates the distribution and scheduling of application containers across a cluster in a more efficient way. Kubernetes is an open-source platform and is production-ready.

A Kubernetes cluster consists of two types of resources:

​	The Control Plane coordinates the cluster

​	Nodes are the workers that run applications

The Control Plane is responsible for managing the cluster. The Control Plane coordinates all activities in your cluster, such as scheduling applications, maintaining applications desired state, scaling applications, and rolling out new updates.

A node is a VM or a physical computer that serves as a worker machine in a Kubernetes cluster. Each node has a Kubelet, which is an agent for managing the node and communicating with the Kubernetes control plane. The node should also have tools for handling container operations, such as containerd or Docker. A Kubernetes cluster that handles production traffic should have a minimum of three nodes.

When you deploy applications on Kubernetes, you tell the control plane to start the application containers. The control plane schedules the containers to run on the cluster’s nodes. The nodes communicate with the control plane using the Kubernetes API, which the control plane exposes. End users can also use the Kubernetes API directly to interact with the cluster.

A Kubernetes cluster can be deployed on either physical or virtual machines. To get started with Kubernetes development, you can use Minikube. 

Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. Minikube is available for Linux, macOS, and Windows systems. The Minikube CLI provides basic bootstrapping operations for working with your cluster, including start, stop, status, and delete. For this tutorial, however, you’ll use a provided online terminal with Minikube pre-installed.

Now that you know what Kubernetes is, lets go to the online tutorial and start our first cluster!

=======================================================================================

Module 1 - Create a Kubernetes cluster

The goal of this interactive scenario is to deploy a local development Kubernetes cluster using Minikube.

Step 1:

Cluster up and running.

##### To check Minikube version : minikube version

$ minikube version
minikube version: v1.18.0
commit: ec61815d60f66a6e4f6353030a40b12362557caa-dirty

##### To start the cluster : minikube start

* $ minikube start
  * minikube v1.18.0 on Ubuntu 18.04 (kvm/amd64)
  * Using the none driver based on existing profile

  X The requested memory allocation of 2200MiB does not leave room for system overhead (total system memory: 2460MiB). You may face stability issues.
  * Suggestion: Start minikube with less memory allocated: 'minikube start --memory=2200mb'

  * Starting control plane node minikube in cluster minikube
  * Running on localhost (CPUs=2, Memory=2460MB, Disk=194868MB) ...
  * OS release is Ubuntu 18.04.5 LTS
  * Preparing Kubernetes v1.20.2 on Docker 19.03.13 ...
    - kubelet.resolv-conf=/run/systemd/resolve/resolv.conf
    - Generating certificates and keys ...
    - Booting up control plane ...
    - Configuring RBAC rules ...
  * Configuring local host environment ...
  * Verifying Kubernetes components...
    - Using image gcr.io/k8s-minikube/storage-provisioner:v4
  * Enabled addons: default-storageclass, storage-provisioner
  * Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

Great! You now have a running Kubernetes cluster in your online terminal. Minikube started a virtual machine for you, and a Kubernetes cluster is now running in that VM.

##### Cluster version

To interact with Kubernetes during this bootcamp we’ll use the command line interface, kubectl. We’ll explain kubectl in detail in the next modules, but for now, we’re just going to look at some cluster information. To check if kubectl is installed you can run the kubectl version 

$ kubectl version
Client Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.4", GitCommit:"e87da0bd6e03ec3fea7933c4b5263d151aafd07c", GitTreeState:"clean", BuildDate:"2021-02-18T16:12:00Z", GoVersion:"go1.15.8", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.2", GitCommit:"faecb196815e248d3ecfb03c680a4507229c2a56", GitTreeState:"clean", BuildDate:"2021-01-13T13:20:00Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"linux/amd64"}

Ok, Kubectl is configured and we can see both the version of the client and as well as the server. The client version is the kubectl version; the server version is the Kubernetes version installed on the master. You can also see the details about the build.

##### Cluster details

Let’s view the cluster details. We’ll do that by running kubectl cluster-info:

$ kubectl cluster-info
Kubernetes control plane is running at https://172.17.0.21:8443
KubeDNS is running at https://172.17.0.21:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

During this tutorial, we’ll be focusing on the command line for deploying and exploring our application. To view the nodes in the cluster, run the kubectl get nodes command:

$ kubectl get nodes
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   16m   v1.20.2

This command shows all nodes that can be used to host our applications. Now we have only one node, and we can see that its status is ready (it is ready to accept applications for deployment).





