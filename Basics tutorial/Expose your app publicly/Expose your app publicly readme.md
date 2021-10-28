https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/

##### Using a Service to Expose Your App

Objectives

​	Learn about a Service in Kubernetes.

​	Understand how labels and LabelSelector objects relate to a Service.

​	Expose an application outside a Kubernetes cluster using a Service.

##### Overview of Kubernetes Services

Kubernetes Pods are mortal. Pods in fact have a lifecycle. When a worker node dies, the Pods running on the Node are also lost. A ReplicaSet might then dynamically drive the cluster back to desired state via creation of new Pods to keep your application running. As another example, consider an image-processing backend with 3 replicas. Those replicas are exchangeable; the front-end system should not care about backend replicas or even if a Pod is lost and recreated. That said, each Pod in a Kubernetes cluster has a unique IP address, even Pods on the same Node, so there needs to be a way of automatically reconciling changes among Pods so that your applications continue to function.

A Service in Kubernetes is an abstraction which defines a logical set of Pods and a policy by which to access them. Services enable a loose coupling between dependent Pods. A Service is defined using YAML (preferred) or JSON, like all Kubernetes objects. The set of Pods targeted by a Service is usually determined by a LabelSelector (see below for why you might want a Service without including selector in the spec).

Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service. Services allow your applications to receive traffic. Services can be exposed in different ways by specifying a type in the ServiceSpec:

##### ClusterIP (default)

Exposes the Service on an internal IP in the cluster. This type makes the Service only reachable from within the cluster.

##### NodePort

Exposes the Service on the same port of each selected Node in the cluster using NAT. Makes a Service accessible from outside the cluster using <NodeIP>: <NodePort> Superset of ClusterIP.

##### LoadBalancer

Creates an external load balancer in the current cloud (if supported) and assigns a fixed, external IP to the Service. Superset of NodePort.

##### ExternalName

Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value. No proxying of any kind is set up. This type requires v1.7 or higher of kube-dns , CoreDNS version 0.0.8 or higher.

More information about the different types of Services can be found in the Using Source IP tutorial. Also see Connecting Applications with Services.

Additionally, note that there are some use cases with Services that involve not defining selector in the spec. A Service created without selector will also not create the corresponding Endpoints object. This allows users to manually map a Service to specific endpoints. Another possibility why there may be no selector is you are strictly using the type: ExternalName 



##### Services and Labels

A Service routes traffic across a set of Pods. Services are the abstraction that allow pods to die and replicate in Kubernetes without impacting your application. Discovery and routing among dependent Pods (such as the frontend and backend components in an application) is handled by Kubernetes Services.

Services match a set of Pods using labels and selectors, a grouping primitive that allows logical operation on objects in Kubernetes. Labels are key/value pairs attached to objects and can be used in any number of ways:

​	Designate objects for development, test, and production.

​	Embed version tags.

​	Classify an object using tags.

Labels can be attached to objects at creation time or later on. They can be modified at any time. Let’s expose our application now using a Service and apply some labels.



##### Interactive Tutorial - Exposing Your App

In this scenario you will learn how to expose Kubernetes applications outside the cluster using the kubectl expose command. You will also learn how to view and apply labels to objects with the kubectl label command.

##### Step 1 Create a new service

Let’s verify that our application is running. We’ll use the kubectl get command and look for existing Pods:

##### kubectl get pods

Next, let’s list the current Services from our cluster:

##### kubectl get services

We have a Service called kubernetes that is created by default when minikube starts the cluster. To create a new service and expose it to external traffic we’ll use the expose command with NodePort as parameter (minikube does not support the LoadBalancer option yet).

##### kubectl expose deployment/kubernetes-bootcamp --type=“NodePort” --port 8080

Let’s run again the get services command:

##### kubectl get services

o/p

NAME                  TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
kubernetes            ClusterIP   10.96.0.1     <none>        443/TCP          16m
kubernetes-bootcamp   NodePort    10.97.95.74   <none>        8080:31563/TCP   2s

We have now a running Service called kubernetes-bootcamp. Here we see that the Service received a unique cluster-IP, an internal port and an external-IP (the IP of the Node).

To find out what port was opened externally (by the NodePort option) we’ll run the describe service command:

##### kubectl describe services/kubernetes-bootcamp

o/p:

Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   app=kubernetes-bootcamp
Annotations:              <none>
Selector:                 app=kubernetes-bootcamp
Type:                     NodePort
IP Families:              <none>
IP:                       10.97.95.74
IPs:                      10.97.95.74
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31563/TCP
Endpoints:                172.18.0.4:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

Create an environment variable called NODE_PORT that has the value of the Node port assigned:

##### export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT

o/p:

$ echo NODE_PORT=$NODE_PORT
NODE_PORT=31563

Now we can test that the app is exposed outside of the cluster using curl, the IP of the Node and the externally exposed port:

##### curl $(minikube ip):$NODE_PORT

o/p:

Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-xt8m2 | v=1

And we get a response from the server. The Service is exposed.





##### Step 2 : Using labels

The Deployment created automatically a label for our Pod. With describe deployment command you can see the name of the label:

##### kubectl describe deployment

o/p:

Name:                   kubernetes-bootcamp
Namespace:              default
CreationTimestamp:      Wed, 27 Oct 2021 09:01:35 +0000
Labels:                 app=kubernetes-bootcamp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=kubernetes-bootcamp
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
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
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   kubernetes-bootcamp-fb5c67579 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  51s   deployment-controller  Scaled up replica set kubernetes-bootcamp-fb5c67579 to 1

Let’s use this label to query our list of Pods. We’ll use the kubectl get pods command with -l as a parameter, followed by the label values:

##### kubectl get pods -l app=kubernetes-bootcamp

o/p:

NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-fb5c67579-hkzlq   1/1     Running   0          3m4s

You can do the same to list the existing services:

##### kubectl get services -l app=kubernetes-bootcamp

NAME                  TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes-bootcamp   NodePort   10.98.81.139   <none>        8080:30208/TCP   4m15s

Get the name of the Pod and store it in the POD_NAME environment variable:

##### export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME

o/p

Name of the Pod: kubernetes-bootcamp-fb5c67579-hkzlq
$ 

To apply a new label we use the label command followed by the object type, object name and the new label:

##### kubectl label pods $POD_NAME version=v1

o/p

pod/kubernetes-bootcamp-fb5c67579-hkzlq labeled

This will apply a new label to our Pod (we pinned the application version to the Pod). and we can check it with the describe pod command:

##### kubectl describe pods $POD_NAME

Namespace:    default
Priority:     0
Node:         minikube/172.17.0.77
Start Time:   Wed, 27 Oct 2021 09:01:46 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=fb5c67579
​              version=v1
Annotations:  <none>
Status:       Running
IP:           172.18.0.3
IPs:
  IP:           172.18.0.3
Controlled By:  ReplicaSet/kubernetes-bootcamp-fb5c67579
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://80f5cca90340fa9b40eef0f683210198b637b6e6ee1d374e68f103307536af79
​    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Wed, 27 Oct 2021 09:02:21 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-rkwb5 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-rkwb5:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-rkwb5
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message

----    ------     ----  ----               -------
  Normal  Scheduled  16m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-fb5c67579-hkzlq to minikube
  Normal  Pulled     16m   kubelet            Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal  Created    15m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    15m   kubelet            Started container kubernetes-bootcamp



We see here that the label is attached now to our Pod. And we can query now the list of pods using the new label:

##### kubectl get pods -l version=v1

NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-fb5c67579-hkzlq   1/1     Running   0          17m



##### Step 3 Deleting a Service

To delete Services you can use the delete service command. Labels can be used also here:

##### kubectl delete service -l app=kubernetes-bootcamp

confirms that the service is gone:

##### kubectl get services

o/p

NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   20m

This confirms that our Service was removed. To confirm that route is not exposed anymore you can curl the previously exposed IP and port:

##### curl $(minikube ip):$NODE_PORT

This proves that the app is not reachable anymore from outside of the cluster. You can confirm that the app is still running with the curl inside the pod:

##### kubectl exec -ti $POD_NAME -- curl localhost:8080

Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-fb5c67579-hkzlq | v=1

We see here that the application is up. This is because the Deployment is managing the application. To shout down the application, you would need to delete the Deployment as well.





