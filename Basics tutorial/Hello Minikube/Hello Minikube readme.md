https://kubernetes.io/docs/tutorials/hello-minikube/

##### Hello Minikube

This tutorial shows you how to run a sample app on Kubernetes using Minikube and Katacoda. Katacoda provides a free, in-browser Kubernetes environment.

Objectives

​	Deploy a sample application to Minikube.

​	Run the app.

​	View application logs.

Before you begin

This tutorial provides a container image that uses NGINX to echo back all the requests.

##### Create a Minikube cluster

We can launch terminal online.

Note : If you installed Minikube locally, run minikube start. Before you run minikube dashboard, you should open a new terminal, start minikube dashboard there, and then switch back to the main terminal.

2 - Open the Kubernetes dashboard in a browser 

minikube dashboard (in laptop if minikube installed)

3 - Katacoda environment only (online terminal): At the top of the terminal pane, click the plus sign, and then click Select port to view on Host 1.

4 - Katacoda environment only: Type 30000, and then click Display Port.

Note :

The dashboard command enables the dashboard add-on and opens the proxy in the default web browser. You can create Kubernetes resources on the dashboard such as Deployment and Service.

If you are running in an environment as root, see Open Dashboard with URL.

By default, the dashboard is only accessible from within the internal Kubernetes virtual network. The dashboard command creates a temporary proxy to make the dashboard accessible from outside the Kubernetes virtual network.

To stop the proxy, run Ctrl+C to exit the process. After the command exits, the dashboard remains running in the Kubernetes cluster. You can run the dashboard command again to create another proxy to access the dashboard.

##### Open Dashboard with URL

If you don’t want to open a web browser, run the dashboard command with the -- url flag to emit the URL:

minikube dashboard --url

##### Create a Deployment

A Kubernetes Pod is a group of one or more Containers, tied together for the purposes of administration and networking. The Pod in this tutorial has only one Container. A Kubernetes Deployment checks on the health of your Pod and restarts the Pod’s Container if it terminates. Deployments are the recommended way to manage the creation and scaling of Pods.

1 - Use the kubectl create command to create a Deployment that manages a Pod. The Pod runs a Container based on the provided Docker image.

##### kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4

2 - View the Deployment:

##### kubectl get deployments

Output is similar to

NAME		READY		UP-TO-DATE		AVAILABLE		AGE

hello-node     1/1                  1                              1                              1m

3 - View the Pod:

##### kubectl get pods

The output is similar to:

NAME								READY		STATUS		RESTARTS		AGE

hello-node-5f7....                                          1/1                  Running         0                              1m

4 - View cluster events:

##### kubectl get events

5 - View the kubectl configuration:

##### kubectl config view



##### Create a Service

By default, the Pod is only accessible by its internal IP address within the Kubernetes cluster. To make the hello-node Container accessible from outside the Kubernetes virtual network, you have to expose the Pod as a Kubernetes Service.

1 - Expose the Pod to the public internet using the kubectl expose command:

##### kubectl expose deployment hello-node --type=LoadBalancer --port=8080

The --type==LoadBalancer flag indicates that you want to expose your Service outside of the cluster.

The application code inside the image k8s.gcr.io/echoserver only listens on TCP port 8080. If you used kubectl expose to expose a different port, clients could not connect to that other port.

2 - View the Service you created:

##### kubectl get services

The output is similar to:

NAME			TYPE			CLUSTER-IP			EXTERNAL-IP			PORT(S)

hello-node              LoadBalancer       10.108.144.78               <pending>                   8080:30369/TCP

kubernetes              ClusterIP               10.96.0.1                        <none>                          443/TCP

On cloud providers that support load balancers, an external IP address would be provisioned to access the Service. On minikube, the LoadBalancer type makes the Service accessible through the minikube service command.

3 - Run the following command:

##### minikube service hello-node

4 - Katacoda environment only: Click the plus sign, and then click Select port to view on Host 1.

5 - Katacoda environment only: Note the 5-digit port number displayed opposite to 8080 in services output. This port number is randomly generated and it can be different for you. Type your number in the port number text box, then click Display Port. Using this example from earlier, you would type 30369.

This opens up a browser window that serves your app and shows the app’s response.

##### Enable addons

The minikube tool indicates a set of built-in addons that can be enabled, disabled, and opened in the local Kubernetes environment.

1 - List the currently supported addons:

##### minikube addons list

2 - Enable an addon, for example, metrics-server:

##### minikube addons enable metrics-server

The output is similar to:

The ‘metrics-server’ addon is enabled.

3 - View the Pod and Service you created:

##### kubectl get pod, svc -n kube-system

4 - Disable metrics-server:

##### minikube addons disable metrics-server

The output is similar to:

metrics-server was successfully disabled.



#### Clean up

Now you can clean up the resources you created in your cluster:

##### kubectl delete service hello-node

##### kubectl delete deployment hello-node

Optionally, stop the Minikube virtual machine (VM):

##### minikube stop

Optionally, delete the Minikube VM

##### minikube delete



