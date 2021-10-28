https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/

##### Viewing Pods and Nodes

Objectives

​	Learn about Kubernetes Pods.

​	Learn about Kubernetes Nodes.

​	Troubleshoot deployed applications.

##### Kubernetes Pods

When you created a Deployment in Module 2, Kubernetes created a Pod to host your application instance. A Pod is a Kubernetes abstraction that represents a group of one or more application containers (such as Docker), and some shared resources for those containers. Those resources include:

​	Shared storage, as Volumes.

​	Networking, as a unique cluster IP address.

​	Information about how to run each container, such as the container image version or specific ports to use.

A Pod models an application-specific “logical host” and can contain different application containers which are relatively tightly coupled. For example, a Pod might include both the container with your Node.js app as well as a different container that feeds the data to be published by the Node.js webserver. The containers in a Pod share an IP Address and port space, are always co-located and co-scheduled, and run in a shared context on the same Node.

Pods are the atomic unit on the Kubernetes platform. When we create a Deployment on Kubernetes, that Deployment creates Pods with containers inside them (as opposed to creating containers directly). Each Pod is tied to the Node where it is scheduled, and remains there until termination (according to restart policy) or deletion. In case of a Node failure, identical Pods are scheduled on other available Nodes in the cluster.



##### Nodes

A Pod always runs on a Node. A Node is a worker machine in Kubernetes and may be either a virtual or a physical machine, depending on the cluster. Each node is managed by the control plane. A Node can have multiple Pods, and the Kubernetes control plane automatically handles scheduling the pods across the Nodes in the cluster. The control plane’s automatic scheduling takes into account the available resources on each Node.

Every Kubernetes Node runs at least:

​	Kubelet, a process responsible for communication between the Kubernetes control plane and the Node; it manages the Pods and the containers running on a machine.

​	A container runtime (like Docker) responsible for pulling the container image from a registry, unpacking the container, and running the application.



##### Troubleshooting with kubectl

In Module 2, you used kubectl command-line interface. You’ll continue to use it in Module 3 to get information about deployed applications and their environments. The most common operations can be done with the following kubectl commands:

​	kubectl get - list resources

​	kubectl describe - show detailed information about a resource

​	kubectl logs - print the logs from a container in a pod

​	kubectl exec - execute a command on a container in a pod

You can use these commands to see when applications were deployed, what their current statuses are, where they are running and what their configurations are.

Now that we know more about our cluster components and the command line, let’s explore our application.



##### Module 3 - Explore your app

In this scenario you will learn how to troubleshoot Kubernetes applications using the kubectl get, describe, logs and exec commands.

Step 1 Check application configuration

Let’s verify that the application we deployed in the previous scenario is running. We’ll use the kubectl get command and look for existing Pods:

##### kubectl get pods

Next, to view what containers are inside that Pod and what images are used to build those containers we run the describe pods command.

##### kubectl describe pods

We see here details about the Pod’s container: IP address, the ports used and a list of events related to the lifecycle of the Pod.

The output of the describe command is extensive and covers some concepts that we didn’t explain yet, but don’t worry, they will become familiar by the end of this bootcamp.

Note: the describe command can be used to get detailed information about most of the kubernetes primitives: node, pods, deployments. The describe output is designed to be human readable, not to scripted against.



##### Step 2 Show the app in the terminal

Recall that Pods are running in an isolated, private network - so we need to proxy access to them so we can debug and interact with them. To do this, we’ll use the kubectl proxy command to run a proxy in a second terminal window. Click on the command below to automatically open a new terminal and run the proxy:



Now again, we’ll get the Pod name and query that pod directly through the proxy. To get the Pod name and store it in the POD_NAME environment variable:

export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME

Name of the Pod: kubernetes-bootcamp-fb5c67579-p62nk

To see the output of our application, run a curl request.

curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/

o/p : Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-p62nk | v=1

The url is the route to the API of the Pod.



##### Step 3 View the container logs

Anything that the application would normally send to STDOUT becomes logs for the container within the Pod. We can retrieve these logs using the kubectl logs command:

##### kubectl logs $POD_NAME

O/p 

Kubernetes Bootcamp App Started At: 2021-10-26T15:05:34.770Z | Running On:  kubernetes-bootcamp-fb5c67579-p62nk 

Running On: kubernetes-bootcamp-fb5c67579-p62nk | Total Requests: 1 | App Uptime: 630.648 seconds | Log Time: 2021-10-26T15:16:05.419Z
Running On: kubernetes-bootcamp-fb5c67579-p62nk | Total Requests: 2 | App Uptime: 637.393 seconds | Log Time: 2021-10-26T15:16:12.163Z
Running On: kubernetes-bootcamp-fb5c67579-p62nk | Total Requests: 3 | App Uptime: 646.458 seconds | Log Time: 2021-10-26T15:16:21.228Z
Running On: kubernetes-bootcamp-fb5c67579-p62nk | Total Requests: 4 | App Uptime: 1093.5 seconds | Log Time: 2021-10-26T15:23:48.270Z

Note: We don’t need to specify the container name, because we only have one container inside the pod.



##### Step 4 Executing command on the container

We can execute commands directly on the container once the Pod is up an running. For this, we use the exec command and use the name of the Pod as a parameter. Let’s list the environment variables:

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=kubernetes-bootcamp-fb5c67579-p62nk
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
NPM_CONFIG_LOGLEVEL=info
NODE_VERSION=6.3.1
HOME=/root

Again, worth mentioning that the name of the container itself can be omitted since we only have a single container in the Pod.

Next let’s start a bash session in the Pod’s container:

##### kubectl exec -ti $POD_NAME -- bash

root@kubernetes-bootcamp-fb5c67579-p62nk:/# 

We have now an open console on the container where we run our NodeJS application. The source code of the app is in the server.js file:

##### cat server.js

You can check that the application is up by running a curl command:

##### curl localhost:8080

Note: here we used localhost because we executed the command inside the NodeJS Pod. If you cannot connect to localhost:8080, check to make sure you have run the kubectl exec command and are launching the command from within the Pod.

To close your container connection type exit





