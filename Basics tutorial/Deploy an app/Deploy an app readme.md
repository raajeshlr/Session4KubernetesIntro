https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/

##### Using Kubectl to Create a Deployment

Objectives

​	Learn about application Deployments.

​	Deploy your first app on Kubernetes with Kubectl.

##### Kubernetes Deployments

Once you have a running Kubernetes cluster, you can deploy your containerized applications on top of it. To do so, you create a Kubernetes Deployment configuration. The Deployment instructs Kubernetes how to create and update instances of your application. Once you’ve created a Deployment, the Kubernetes control plane schedules the application instances included in that Deployment to run on individual Nodes in the cluster.

Once the application instances are created, a Kubernetes Deployment Controller continuously monitors those instances. If the Node hosting an instance goes down or is deleted, the Deployment controller replaces the instance with an instance on another Node in the cluster. This provides a self-healing mechanism to address machine failure or maintenance.

In a pre-orchestration world, installation scripts would often be used to start applications, but they did not allow recovery from machine failure. By both creating your application instances and keeping them running across Nodes, Kubernetes Deployments provide a fundamentally different approach to application management.



You can create and manage a Deployment by using the Kubernetes command line interface, Kubectl. Kubectl uses the Kubernetes API to interact with the cluster. In this module, you’ll learn the most common Kubectl commands needed to create Deployments that run your applications on a Kubernetes cluster.

When you create a Deployment, you’ll need to specify the container image for your application and the number of replicas that you want to run. You can change that information later by updating your Deployment; Module 5 and 6 of the bootcamp discuss how you can scale and update your Deployments.

For your first Deployment, you’ll use a hello-node application packaged in a Docker container that uses NGINX to echo back all the requests. (If you didn’t already try creating a hello-node application and deploying it using a container, you can do that first by following the instructions from the Hello Minikube tutorial)

Now that you know what Deployments are, let’s go to the online tutorial and deploy our first app!

##### Module 2 - Deploy an app

Step 1 of 3 :

Kubectl basics

Like minikube, kubectl comes installed in the online terminal. Type kubectl in the terminal to see its usage. The common format of a kubectl command is: kubectl action resource. This performs the specified action (like create, describe) on the specified resource (like node, container), You can use --help after the command to get additional info about the possible parameters (kubectl get nodes --help)

Check that kubectl is configured to talk to your cluster, by running the kubectl version command:

##### kubectl version

OK, kubectl is installed and you can see both the client and the server versions.

To view the nodes in the cluster, run the kubectl get nodes command:

##### kubectl get nodes

Here we see the available nodes (1 in our case - minikube). Kubernetes will choose where to deploy our application based on Node available resources.



##### Deploy our app

Let’s deploy our first app on Kubernetes with the 

##### kubectl create deployment

command. We need to provide the deployment name and app image location (include the full repository url for images hosted outside Docker hub).

##### kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1

Great! You just deployed your first application by creating a deployment. This performed a few things for you:

​	Searched for a suitable node where an instance of the application could be run (we have only 1 			available node)

​	Scheduled the application to run on that Node.

​	configured the cluster to reschedule the instance on a new Node when needed.

To list your deployments use the get deployments command:

##### kubectl get deployments

We see that there is 1 deployment running a single instance of your app The instance is running inside a Docker container on your node.



##### View our app

Pods that are running inside Kubernetes are running on a private, isolated network. By default they are visible from other pods and services within the same kubernetes cluster, but not outside that network. When we use kubectl, we’re interacting through an API endpoint to communicate with our application.

We will cover other options on how to expose your application outside the kubernetes cluster in Module 4.

The kubectl command can create a proxy that will forward communications into the cluster-wide, private network. The proxy can be terminated by pressing control-C and won’t show any output while its running.

We will open a second terminal window to run the proxy.

echo -e "\n\n\n\e[92mStarting Proxy. After starting it will not output a response. Please click the first Terminal Tab\n"; 
kubectl proxy

We now have a connection between our host (the online terminal) and the Kubernetes cluster. The proxy enables direct access to the API from these terminals.

You can see all those APIs hosted through the proxy endpoint. For example, we can query the version directly through the API using the curl command.

curl http://localhost:8001/version

Note : Check the top of the terminal. The proxy was run in a new tab (Terminal 2), and the recent commands were executed the original tab (Terminal 1). The proxy still runs in the second tab, and this allowed our curl command to work using Localhost:8001

If port 8001 is not accessible, ensure that the kubectl proxy started above is running.

The API server will automatically create an endpoint for each pod, based on the pod name, that is also accessible through the proxy.

First we need to get the Pod name, and we’ll store in the environment variable

POD_NAME:

export POD_NAME=$(kubectl get pods -o go-template --tempate '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
$ echo Name of the Pod: $POD_NAME
Name of the Pod: kubernetes-bootcamp-57978f5f5d-78qnv

You can access the Pod through the API by running.

curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/

In order for the new deployment to be accessible without using the Proxy, a Service is required which will be explained in the next modules.

