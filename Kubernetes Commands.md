Below are the commands collated from all the tutorial links.

To deploy our app : we need Minikube and Kubectl.

What is Minikube - It is a tool that lets you run Kubernetes, it has be installed.

What is Kubectl - Kubernetes command-line tool, allows you to run commands against Kubernetes clusters.

##### 1. To check Minikube version - minikube version

Sample Output :

minikube version: v1.18.0
commit: ec61815d60f66a6e4f6353030a40b12362557caa-dirty

##### 2. To start the cluster - minikube start

It created a VM and deployed a simple cluster containing only one node.

Now, let’s start interacting with Kubernetes cluster and then deploy our app.

##### 3. To check Kubectl version - kubectl version

##### 4. To check cluster-info - kubectl cluster-info

Sample Output :

Kubernetes control plane is running at https://172.17.0.63:8443
KubeDNS is running at https://172.17.0.63:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

Now, let’s check the nodes in the deployed cluster.

##### 5. To check nodes - kubectl get nodes

Sample Output : 

NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   10m   v1.20.2

So by default, minikube node is available, when we deploy our app, it gets deployed on that Node.

Let’s create a Deployment!

We only need a Image, everything else will be taken care by kubectl.

##### 6. Create Deployment - kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1

Here “Kubernetes-bootcamp” is Deployment Name.

Image is - from gcr.io/google-samples/kubernetes-bootcamp:v1

Sample Output : 

deployment.apps/kubernetes-bootcamp created

What it has done ? It searched for suitable Node (we have only one Node which is minikube), scheduled our app to run on that Node, configured the cluster to reschedule the instance on a new Node when needed.

##### 7. List Deployments - kubectl get deployments

Sample Output :

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           3m44s

Once deployed, it containerized our image, created Pod.

##### 8. To check Pods  - kubectl get pods

Sample Output :

NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-4vtn4   1/1     Running   0          7m42s

It created a Pod for us, with the same Deployment Name and some random number, then again some random numbers.

##### 9. To describe pods - kubectl describe pods

It tells Node, IP and Port and other details.

##### Interacting with app:

Usually Pods runs in private, isolated network and only visible to other pods and services within the K8s cluster and there are two ways we can interact with our app.

- Through an API endpoint.
- Exposing as a Service.

##### First Method : Through an API endpoint

Kubectl command can create a proxy that will forward communications into the cluster-wide, private network. 

We should open the second terminal for creating the proxy.

##### 10. To create proxy - echo -e

Starting Proxy. After starting it will not output a response. Please click the first Terminal Tab

$ kubectl proxy
Starting to serve on 127.0.0.1:8001

We now have a connection between our host and K8s cluster. The proxy enables direct access to the API from these terminals. Now we can curl to interact with our app.

##### curl http://localhost:8001/version

Sample Output :

{
  "major": "1",
  "minor": "20",
  "gitVersion": "v1.20.2",
  "gitCommit": "faecb196815e248d3ecfb03c680a4507229c2a56",
  "gitTreeState": "clean",
  "buildDate": "2021-01-13T13:20:00Z",
  "goVersion": "go1.15.5",
  "compiler": "gc",
  "platform": "Linux/amd64"
}

We can also export the Pod Name and we can curl and get the details of that particular Pod like below.

##### curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/

##### 12. View logs using Pod Name - kubectl logs $POD_NAME

##### 13. To execute command on the container - kubectl exec $POD_NAME -- env

##### 14. To start the bash session in the Pod’s container - kubectl exec -ti $POD_NAME --bash

Once we inside the container, we can do commands like cat server.js to view that file if its available, curl localhost:8000 to use the app.

We used localhost as we are inside the container.



##### 							Second Method : Exposing as a Service

By default, we will have a service called kubernetes.

##### 15. Check services - kubectl get services

Sample Output :

NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   20m

##### 16. Create new service for our deployment - kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080

We are creating service for our deployment kubernetes-bootcamp here.

It exposes our app to external traffic.

Now if we run kubectl get services, the output would be:

NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes            ClusterIP   10.96.0.1      <none>        443/TCP          24m
kubernetes-bootcamp   NodePort    10.108.53.80   <none>        8080:32386/TCP   2m17s

Note : Deployment Name is same as Services Name (kubernetes-bootcamp).

##### 17. To describe service - kubectl describe services/kubernetes-bootcamp

Now we can get the Exposed Port number of our app from the output of describe command.

export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT

So..

Now we can curl our Exposed app using Port Number!

##### curl $(minikube ip):$NODE_PORT

Sample Output : 

Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-wd7q8 | v=1

##### 18. Labelling a Pod

We can also label our Pod and use it with the below commands.

kubectl label pods $POD_NAME version=v1

kubectl get pods -l version=v1

##### 19. Deleting a Service - kubectl delete service -l app=kubernetes-bootcamp

Once we see the deployments list like below.

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           5m47s

- *NAME* lists the names of the Deployments in the cluster.

- *READY* shows the ratio of CURRENT/DESIRED replicas

- *UP-TO-DATE* displays the number of replicas that have been updated to achieve the desired state.

- *AVAILABLE* displays how many replicas of the application are available to your users.

- *AGE* displays the amount of time that the application has been running.

##### 20. How to see replicas list - kubectl get rs

Sample Output :

NAME                            DESIRED   CURRENT   READY   AGE
kubernetes-bootcamp-fb5c67579   1         1         1       9m3s

- *DESIRED* displays the desired number of replicas of the application, which you define when you create the Deployment. This is the desired state.
- *CURRENT* displays how many replicas are currently running.

##### 21. How to scale to 4 replicas - kubectl scale deployments/kubernetes-bootcamp --replicas=4

Now if we check deployments, the output will be

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           10m

There are four replicas now.

We can also check pods, that would be 4 now.

##### kubectl get pods -o wide

NAME                                  READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
kubernetes-bootcamp-fb5c67579-pkvl2   1/1     Running   0          56s   172.18.0.7   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-q464h   1/1     Running   0          10m   172.18.0.6   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-tm5fc   1/1     Running   0          56s   172.18.0.9   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-vfg46   1/1     Running   0          56s   172.18.0.8   minikube   <none>           <none>

Now if we curl, we can see getting responses from different pods.

We can also scale down using command - kubectl scale deployments/kubernetes-bootcamp --replicas=2

##### 22. Updating our app (new image) - kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2

We can also rollout to go back to the previous version.

kubectl rollout undo deployments/kubernetes-bootcamp









































