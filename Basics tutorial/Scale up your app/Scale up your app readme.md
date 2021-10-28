https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/

##### Running Multiple Instances of Your App

Objectives

​	Scale an app using kubectl.

##### Scaling an application

In the previous modules we created a Deployment, and then exposed it publicly via a Service. The Deployment created only one Pod for running our application. When traffic increases, we will need to scale the application to keep up with user demand.

Scaling is accomplished by changing the number of replicas in a Deployment.

Scaling out a Deployment will ensure new Pods are created and scheduled to Nodes with available resources. Scaling will increase the number of Pods to the new desired state. Kubernetes also supports autoscaling of Pods, but it is outside of the scope of this tutorial. Scaling to zero is also possible, and it will terminate all Pods of the specified Deployment.

Running multiple instances of an application will require a way to distribute the traffic to all of them. Services have an integrated load-balancer that will distribute network traffic to all Pods of an exposed Deployment. Services will monitor continuously the running Pods using endpoints, to ensure the traffic is sent only to available Pods.

Once you have multiple instances of an Application running, you would be able to do Rolling updates without downtime. We’ll cover that in the next module. Now, let’s go to the online terminal and scale our application.



##### Module 5 - Scale up your app

The goal of this interactive scenario is to scale a deployment with kubectl state and to see the load balancing in action

##### Step 1 : Scaling a deployment

To list your deployments use the get deployments command

##### kubectl get deployments

The output should be similar to:

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           14s

We should have 1 Pod. If not, run the command again. This shows:

​	NAME lists the names of the Deployments in the cluster.

​	READY shows the ratio of CURRENT/DESIRED replicas

​	UP-TO-DATE displays the number of replicas that have been updated to achieve the desired state.

​	AVAILABLE displays how many replicas of the application are available to your users

​	AGE displays the amount of time that the application has been running.

To see the ReplicaSet created by the Deployment, run

##### kubectl get rs

NAME                            DESIRED   CURRENT   READY   AGE
kubernetes-bootcamp-fb5c67579   1         1         1       3m52s

Notice that the name of the ReplicaSet is always formatted as

[DEPLOYMENT-NAME]-[RANDOM-STRING]

The random string is randomly generated and uses the pod-template-hash as a seed.

Two important columns of this command are:

​	DESIRED displays the desired number of replicas of the application, which you define when you create the Deployment. This is the desired state.

​	CURRENT displays how many replicas are currently running.

Next, let’s scale the Deployment to 4 replicas. We’ll use the kubectl scale command, followed by the deployment type, name and desired number of instances:

##### kubectl scale deployments/kubernetes-bootcamp --replicas=4

o/p

4
deployment.apps/kubernetes-bootcamp scaled

To list your Deployments once again, use get deployments:

##### kubectl get deployments

o/p

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           14m
$ 

The change was applied, and we have 4 instances of the application available. Next, let’s check if the number of Pods changed:

##### Normally we will give the deployment name, one replica of pod will be created with “deployment name-some random number-SOME random number” and then if you scale the replicas to 4 lets say, new 4 pods name will be generated in same manner : “deployment name-THAT SAME random number-some random number”, .. but if you see replicas with kubectl get rs command, you can see the “deployment name-THAT SAME random number”

##### kubectl get pods -o wide

o/p

NAME                                  READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
kubernetes-bootcamp-fb5c67579-b7nxr   1/1     Running   0          39m   172.18.0.7   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-kc2dq   1/1     Running   0          39m   172.18.0.9   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-kvbwp   1/1     Running   0          50m   172.18.0.6   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-qjktl   1/1     Running   0          39m   172.18.0.8   minikube   <none>           <none>

e>
$ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           52m
$ kubectl get rs
NAME                            DESIRED   CURRENT   READY   AGE
kubernetes-bootcamp-fb5c67579   4         4         4       51m



There are 4 Pods now, with different IP addresses. The change was registered in the Deployment events log. To check that, use the describe command:

##### kubectl describe deployments/kubernetes-bootcamp

o/p

Name:                   kubernetes-bootcamp
Namespace:              default
CreationTimestamp:      Wed, 27 Oct 2021 15:28:20 +0000
Labels:                 app=kubernetes-bootcamp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=kubernetes-bootcamp
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=kubernetes-bootcamp
  Containers:
   kubernetes-bootcamp:
​    Image:        gcr.io/google-samples/kubernetes-bootcamp:v1
​    Port:         8080/TCP
​    Host Port:    0/TCP
​    Environment:  <none>
​    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason

----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   kubernetes-bootcamp-fb5c67579 (4/4 replicas created)
Events:
  Type    Reason             Age   From                   Message
----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  17s   deployment-controller  Scaled up replica set kubernetes-bootcamp-fb5c67579 to 1
  Normal  ScalingReplicaSet  11s   deployment-controller  Scaled up replica set kubernetes-bootcamp-fb5c67579 to 4
$ kubectl describe deployments/kubernetes-bootcamp
Name:                   kubernetes-bootcamp
Namespace:              default
CreationTimestamp:      Wed, 27 Oct 2021 15:28:20 +0000
Labels:                 app=kubernetes-bootcamp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=kubernetes-bootcamp
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=kubernetes-bootcamp
  Containers:
   kubernetes-bootcamp:
​    Image:        gcr.io/google-samples/kubernetes-bootcamp:v1
​    Port:         8080/TCP
​    Host Port:    0/TCP
​    Environment:  <none>
​    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   kubernetes-bootcamp-fb5c67579 (4/4 replicas created)
Events:
  Type    Reason             Age   From                   Message
----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  29s   deployment-controller  Scaled up replica set kubernetes-bootcamp-fb5c67579 to 1
  Normal  ScalingReplicaSet  23s   deployment-controller  Scaled up replica set kubernetes-bootcamp-fb5c67579 to 4
$ 



You can also view in the output of this command that there are 4 replicas now.



##### Step 2 : Load Balancing

Let’s check that the Service is load-balancing the traffic. To find out the exposed IP and Port we can use the describe service as we learned in the previous Module:

##### kubectl describe services/kubernetes-bootcamp

o/p

Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   app=kubernetes-bootcamp
Annotations:              <none>
Selector:                 app=kubernetes-bootcamp
Type:                     NodePort
IP Families:              <none>
IP:                       10.101.107.177
IPs:                      10.101.107.177
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30941/TCP
Endpoints:                172.18.0.2:8080,172.18.0.7:8080,172.18.0.8:8080 + 1 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

Create an environment variable called NODE_PORT that has a value as the Node port:

##### export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT

o/p:

$ echo NODE_PORT=$NODE_PORT
NODE_PORT=30941

Next, we’ll do a curl to the exposed IP and port. Execute the command multiple times.

##### curl $(minikube ip):$NODE_PORT

$ curl $(minikube ip):$NODE_PORT
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-mdkzp | v=1
$ curl $(minikube ip):$NODE_PORT
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-9zxkr | v=1
$ curl $(minikube ip):$NODE_PORT
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-mdkzp | v=1
$ 



##### Step 3 : Scale Down

To scale down the Service to 2 replicas, run again the scale command:

##### kubectl scale deployments/kubernetes-bootcamp --replicas=2

o/p:

$ kubectl scale deployments/kubernetes-bootcamp --replicas=2
deployment.apps/kubernetes-bootcamp scaled
$ 

List the Deployments to check if the change was applied with the get deployments command:

##### kubectl get deployments

o/p:

$ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   2/2     2            2           39m
$ 

The number of replicas decreased to 2. List the number of Pods, with get pods:

##### kubectl get pods -0 wide

o/p

$ kubectl get pods -o wide
NAME                                  READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
kubernetes-bootcamp-fb5c67579-9zxkr   1/1     Running   0          39m   172.18.0.9   minikube   <none>           <none>
kubernetes-bootcamp-fb5c67579-x5z75   1/1     Running   0          40m   172.18.0.2   minikube   <none>           <none>
$ 

This confirms that 2 Pods are terminated.



