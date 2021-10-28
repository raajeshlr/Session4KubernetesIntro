https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/

##### Performing a Rolling Update

Objectives

​	Perform a rolling update using kubectl.

##### Updating an application

Users expect applications to be available all the time and developers are expected to deploy new versions of them several times a day. In Kubernetes this is done with rolling updates. Rolling updates allow Deployments update to take place with zero downtime by incrementally updating Pods instances with new ones. The new Pods will be scheduled on Nodes with available resources.

In the previous module we scaled our application to run multiple instances. This is a requirement for performing updates without affecting application availability. By default, the maximum number of Pods that can be unavailable during the update and the maximum number of new Pods that can be created, is one. Both options can be configured to either numbers or percentage (of Pods). In Kubernetes, updates are versioned and any Deployment update can be reverted to a previous (stable) version.



Similar to application Scaling, if a Deployment is exposed publicly, the Service will load-balance the traffic only to available Pods during the update. An available Pod is an instance that is available to the users of the application.

Rolling updates allow the following actions:

​	Promote an application from one environment to another (via container image updates).

​	Rollback to previous versions

​	Continuous Integration and Continuous Delivery of applications with zero downtime.

In the following interactive tutorial, we’ll update our application to a new version, and also perform a rollback.



##### Module 6 - Update your app

The goal of this scenario is to update a deployed application with kubectl set image and to rollback with the rollout undo command.

##### Step 1 : Update the version of the app

To list your deployments, run the get deployments command:

##### kubectl get deployments

o/p

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           15m



To list the running Pods, run the get pods command:

##### kubectl get pods

o/p

NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-fb5c67579-6282q   1/1     Running   0          15m
kubernetes-bootcamp-fb5c67579-8crqq   1/1     Running   0          15m
kubernetes-bootcamp-fb5c67579-9htfs   1/1     Running   0          15m
kubernetes-bootcamp-fb5c67579-lhk82   1/1     Running   0          15m



To view the current image version of the app, run the describe pods command and look for the Image field:

##### kubectl describe pods

Name:         kubernetes-bootcamp-fb5c67579-6282q
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.47
Start Time:   Thu, 28 Oct 2021 05:43:58 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=fb5c67579
Annotations:  <none>
Status:       Running
IP:           172.18.0.7
IPs:
  IP:           172.18.0.7
Controlled By:  ReplicaSet/kubernetes-bootcamp-fb5c67579
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://f285a9cdf112c8ff7f3eb0349b1fbaae5d3fda3ae468d6c46cf6266a8e586724
​    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Thu, 28 Oct 2021 05:44:07 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-sjnpj (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-sjnpj:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-sjnpj
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message

----    ------     ----  ----               -------
  Normal  Scheduled  16m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-fb5c67579-6282q to minikube
  Normal  Pulled     16m   kubelet            Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal  Created    16m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    16m   kubelet            Started container kubernetes-bootcamp


Name:         kubernetes-bootcamp-fb5c67579-8crqq
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.47
Start Time:   Thu, 28 Oct 2021 05:43:59 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=fb5c67579
Annotations:  <none>
Status:       Running
IP:           172.18.0.9
IPs:
  IP:           172.18.0.9
Controlled By:  ReplicaSet/kubernetes-bootcamp-fb5c67579
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://287a4d0eac315099e36439a0ee0750a33b0b6834a054fa6d45050990894238c4
​    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Thu, 28 Oct 2021 05:44:07 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-sjnpj (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-sjnpj:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-sjnpj
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
----    ------     ----  ----               -------
  Normal  Scheduled  16m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-fb5c67579-8crqq to minikube
  Normal  Pulled     16m   kubelet            Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal  Created    16m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    16m   kubelet            Started container kubernetes-bootcamp


Name:         kubernetes-bootcamp-fb5c67579-9htfs
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.47
Start Time:   Thu, 28 Oct 2021 05:43:58 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=fb5c67579
Annotations:  <none>
Status:       Running
IP:           172.18.0.2
IPs:
  IP:           172.18.0.2
Controlled By:  ReplicaSet/kubernetes-bootcamp-fb5c67579
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://b5c6499b915ff3631d215f14421124da05c31bf02623b3cf442bb76f435f3093
​    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Thu, 28 Oct 2021 05:44:02 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-sjnpj (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-sjnpj:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-sjnpj
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
----    ------     ----  ----               -------
  Normal  Scheduled  16m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-fb5c67579-9htfs to minikube
  Normal  Pulled     16m   kubelet            Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal  Created    16m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    16m   kubelet            Started container kubernetes-bootcamp


Name:         kubernetes-bootcamp-fb5c67579-lhk82
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.47
Start Time:   Thu, 28 Oct 2021 05:43:59 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=fb5c67579
Annotations:  <none>
Status:       Running
IP:           172.18.0.8
IPs:
  IP:           172.18.0.8
Controlled By:  ReplicaSet/kubernetes-bootcamp-fb5c67579
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://39f7326934542b8b399a551d281c5a00f7263f53d0c5a3c78dc9867efd6df070
​    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Thu, 28 Oct 2021 05:44:07 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-sjnpj (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-sjnpj:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-sjnpj
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
----    ------     ----  ----               -------
  Normal  Scheduled  16m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-fb5c67579-lhk82 to minikube
  Normal  Pulled     16m   kubelet            Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal  Created    16m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    16m   kubelet            Started container kubernetes-bootcamp



To update the image of the application to version 2, use the set image command, followed by the deployment name and the new image version:

##### kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2

o/p

$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
deployment.apps/kubernetes-bootcamp image updated
$ 

The command notified the Deployment to use a different image for your app and initiated a rolling update. Check the status of the new Pods, and view the old one terminating with the get pods command:

o/p

$ kubectl get pods
NAME                                   READY   STATUS        RESTARTS   AGE
kubernetes-bootcamp-7d44784b7c-96vjv   1/1     Running       0          12s
kubernetes-bootcamp-7d44784b7c-fk28v   1/1     Running       0          9s
kubernetes-bootcamp-7d44784b7c-fpxg6   1/1     Running       0          9s
kubernetes-bootcamp-7d44784b7c-sfjq2   1/1     Running       0          12s
kubernetes-bootcamp-fb5c67579-8n892    1/1     Terminating   0          26s
kubernetes-bootcamp-fb5c67579-qhtpw    1/1     Terminating   0          26s
kubernetes-bootcamp-fb5c67579-txbmr    1/1     Terminating   0          26s
kubernetes-bootcamp-fb5c67579-xl4t8    1/1     Terminating   0      

##### Pods are getting terminated and its reducing

$ kubectl get pods
NAME                                   READY   STATUS        RESTARTS   AGE
kubernetes-bootcamp-7d44784b7c-96vjv   1/1     Running       0          97s
kubernetes-bootcamp-7d44784b7c-fk28v   1/1     Running       0          94s
kubernetes-bootcamp-7d44784b7c-fpxg6   1/1     Running       0          94s
kubernetes-bootcamp-7d44784b7c-sfjq2   1/1     Running       0          97s
kubernetes-bootcamp-fb5c67579-xl4t8    0/1     Terminating   0          111s
$ 

NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-7d44784b7c-96vjv   1/1     Running   0          2m8s
kubernetes-bootcamp-7d44784b7c-fk28v   1/1     Running   0          2m5s
kubernetes-bootcamp-7d44784b7c-fpxg6   1/1     Running   0          2m5s
kubernetes-bootcamp-7d44784b7c-sfjq2   1/1     Running   0          2m8s
$ 



##### Step 2 Verify an update

First, check that the app is running. To find the exposed IP and Port, run the describe service command:

##### kubectl describe services/kubectl-bootcamp

o/p:

$ kubectl describe services/kubernetes-bootcamp
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   app=kubernetes-bootcamp
Annotations:              <none>
Selector:                 app=kubernetes-bootcamp
Type:                     NodePort
IP Families:              <none>
IP:                       10.104.197.59
IPs:                      10.104.197.59
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30521/TCP
Endpoints:                172.18.0.10:8080,172.18.0.11:8080,172.18.0.12:8080 + 1 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
$

Create an environment variable called NODE_PORT that has the value of the Node Port assigned:

##### export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT

o/p:

$ echo NODE_PORT=$NODE_PORT
NODE_PORT=30521

Next, do a curl to the exposed IP and port:

##### curl $(minikube ip):$NODE_PORT

o/p:

$ curl $(minikube ip):$NODE_PORT
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-7d44784b7c-96vjv | v=2

Every time you run the curl command, you will hit a different Pod. Notice that all Pods are running the latest version (v2).

You can also confirm the update by running the rollout status command:

##### kubectl rollout status deployments/kubernetes-bootcamp

$ kubectl rollout status deployments/kubernetes-bootcamp
deployment "kubernetes-bootcamp" successfully rolled out
$ 

To view the current image version of the app, run the describe pods command:

##### kubectl describe pods

In the Image field of the output, verify that you are running the latest image version (v2).



##### Step 3 : Rollback an Update

Let’s perform another update, and deploy an image tagged with v10:

##### kubectl set image deployments/kubernetes-bootcamp kubernetes bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10

o/p

$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
deployment.apps/kubernetes-bootcamp image updated

Use get deployments to see the status of the deployment:

##### kubectl get deployments

o/p

$ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   3/4     2            3           39m
$ 

Notice that the output doesn’t list the desired number of available Pods. Run the get pods command to list all Pods:

##### kubectl get pods

o/p

NAME                                   READY   STATUS             RESTARTS   AGE
kubernetes-bootcamp-59b7598c77-kh88g   0/1     ImagePullBackOff   0          15m
kubernetes-bootcamp-59b7598c77-x2fpq   0/1     ImagePullBackOff   0          15m
kubernetes-bootcamp-7d44784b7c-96vjv   1/1     Running            0          39m
kubernetes-bootcamp-7d44784b7c-fk28v   1/1     Running            0          39m
kubernetes-bootcamp-7d44784b7c-sfjq2   1/1     Running            0          39m

Notice that some of the Pods have a status of ImagePullBackOff

To get more insight into the problem, run the describe pods command:

##### kubectl describe pods

o/p:

$ kubectl describe pods
Name:         kubernetes-bootcamp-59b7598c77-kh88g
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.81
Start Time:   Thu, 28 Oct 2021 07:21:46 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=59b7598c77
Annotations:  <none>
Status:       Pending
IP:           172.18.0.2
IPs:
  IP:           172.18.0.2
Controlled By:  ReplicaSet/kubernetes-bootcamp-59b7598c77
Containers:
  kubernetes-bootcamp:
​    Container ID:   
​    Image:          gcr.io/google-samples/kubernetes-bootcamp:v10
​    Image ID:       
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Waiting
​      Reason:       ErrImagePull
​    Ready:          False
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-4j4rz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  default-token-4j4rz:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-4j4rz
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                 From               Message

----     ------     ----                ----               -------
  Normal   Scheduled  16m                 default-scheduler  Successfully assigned default/kubernetes-bootcamp-59b7598c77-kh88g to minikube
  Normal   Pulling    14m (x4 over 16m)   kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v10"
  Warning  Failed     14m (x4 over 16m)   kubelet            Failed to pull image "gcr.io/google-samples/kubernetes-bootcamp:v10": rpc error: code = Unknown desc = Error response from daemon: manifest for gcr.io/google-samples/kubernetes-bootcamp:v10 not found: manifest unknown: Failed to fetch "v10" from request "/v2/google-samples/kubernetes-bootcamp/manifests/v10".
  Warning  Failed     14m (x4 over 16m)   kubelet            Error: ErrImagePull
  Normal   BackOff    14m (x6 over 16m)   kubelet            Back-off pulling image "gcr.io/google-samples/kubernetes-bootcamp:v10"
  Warning  Failed     80s (x65 over 16m)  kubelet            Error: ImagePullBackOff


Name:         kubernetes-bootcamp-59b7598c77-x2fpq
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.81
Start Time:   Thu, 28 Oct 2021 07:21:46 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=59b7598c77
Annotations:  <none>
Status:       Pending
IP:           172.18.0.3
IPs:
  IP:           172.18.0.3
Controlled By:  ReplicaSet/kubernetes-bootcamp-59b7598c77
Containers:
  kubernetes-bootcamp:
​    Container ID:   
​    Image:          gcr.io/google-samples/kubernetes-bootcamp:v10
​    Image ID:       
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Waiting
​      Reason:       ImagePullBackOff
​    Ready:          False
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-4j4rz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  default-token-4j4rz:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-4j4rz
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                 From               Message
----     ------     ----                ----               -------
  Normal   Scheduled  16m                 default-scheduler  Successfully assigned default/kubernetes-bootcamp-59b7598c77-x2fpq to minikube
  Normal   Pulling    14m (x4 over 16m)   kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v10"
  Warning  Failed     14m (x4 over 16m)   kubelet            Failed to pull image "gcr.io/google-samples/kubernetes-bootcamp:v10": rpc error: code = Unknown desc = Error response from daemon: manifest for gcr.io/google-samples/kubernetes-bootcamp:v10 not found: manifest unknown: Failed to fetch "v10" from request "/v2/google-samples/kubernetes-bootcamp/manifests/v10".
  Warning  Failed     14m (x4 over 16m)   kubelet            Error: ErrImagePull
  Normal   BackOff    14m (x6 over 16m)   kubelet            Back-off pulling image "gcr.io/google-samples/kubernetes-bootcamp:v10"
  Warning  Failed     73s (x64 over 16m)  kubelet            Error: ImagePullBackOff

Name:         kubernetes-bootcamp-7d44784b7c-96vjv
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.81
Start Time:   Thu, 28 Oct 2021 06:57:24 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=7d44784b7c
Annotations:  <none>
Status:       Running
IP:           172.18.0.10
IPs:
  IP:           172.18.0.10
Controlled By:  ReplicaSet/kubernetes-bootcamp-7d44784b7c
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://92439d8a1eb87303ebfcdb217f4e84e711ce9b848105afe721380e0c0bc4189e
​    Image:          jocatalin/kubernetes-bootcamp:v2
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:fb1a3ced00cecfc1f83f18ab5cd14199e30adc1b49aa4244f5d65ad3f5feb2a5
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Thu, 28 Oct 2021 06:57:26 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-4j4rz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-4j4rz:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-4j4rz
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message

----    ------     ----  ----               -------
  Normal  Scheduled  40m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-7d44784b7c-96vjv to minikube
  Normal  Pulled     40m   kubelet            Container image "jocatalin/kubernetes-bootcamp:v2" already present on machine
  Normal  Created    40m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    40m   kubelet            Started container kubernetes-bootcamp


Name:         kubernetes-bootcamp-7d44784b7c-fk28v
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.81
Start Time:   Thu, 28 Oct 2021 06:57:27 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=7d44784b7c
Annotations:  <none>
Status:       Running
IP:           172.18.0.13
IPs:
  IP:           172.18.0.13
Controlled By:  ReplicaSet/kubernetes-bootcamp-7d44784b7c
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://4a3708ffbb17605572276e177e6d6b8e5694345505a3cdf4e9c31808705c0043
​    Image:          jocatalin/kubernetes-bootcamp:v2
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:fb1a3ced00cecfc1f83f18ab5cd14199e30adc1b49aa4244f5d65ad3f5feb2a5
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Thu, 28 Oct 2021 06:57:29 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-4j4rz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-4j4rz:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-4j4rz
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
----    ------     ----  ----               -------
  Normal  Scheduled  40m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-7d44784b7c-fk28v to minikube
  Normal  Pulled     40m   kubelet            Container image "jocatalin/kubernetes-bootcamp:v2" already present on machine
  Normal  Created    40m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    40m   kubelet            Started container kubernetes-bootcamp


Name:         kubernetes-bootcamp-7d44784b7c-sfjq2
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.81
Start Time:   Thu, 28 Oct 2021 06:57:24 +0000
Labels:       app=kubernetes-bootcamp
​              pod-template-hash=7d44784b7c
Annotations:  <none>
Status:       Running
IP:           172.18.0.11
IPs:
  IP:           172.18.0.11
Controlled By:  ReplicaSet/kubernetes-bootcamp-7d44784b7c
Containers:
  kubernetes-bootcamp:
​    Container ID:   docker://19f293004734dfade3f5d8c7d7205c301affd9efdb63a7004e112189e9ead859
​    Image:          jocatalin/kubernetes-bootcamp:v2
​    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:fb1a3ced00cecfc1f83f18ab5cd14199e30adc1b49aa4244f5d65ad3f5feb2a5
​    Port:           8080/TCP
​    Host Port:      0/TCP
​    State:          Running
​      Started:      Thu, 28 Oct 2021 06:57:26 +0000
​    Ready:          True
​    Restart Count:  0
​    Environment:    <none>
​    Mounts:
​      /var/run/secrets/kubernetes.io/serviceaccount from default-token-4j4rz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-4j4rz:
​    Type:        Secret (a volume populated by a Secret)
​    SecretName:  default-token-4j4rz
​    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
​                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
----    ------     ----  ----               -------
  Normal  Scheduled  40m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-7d44784b7c-sfjq2 to minikube
  Normal  Pulled     40m   kubelet            Container image "jocatalin/kubernetes-bootcamp:v2" already present on machine
  Normal  Created    40m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    40m   kubelet            Started container kubernetes-bootcamp



In the Events section of the output for the affected Pods, notice that the v10 image version did not exist in the repository.

To roll back the deployment to your last working version, use the rollout undo command:

##### kubectl rollout undo deployment/kubernetes-bootcamp

o/p

$ kubectl rollout undo deployments/kubernetes-bootcamp
deployment.apps/kubernetes-bootcamp rolled back
$ 

The rollout undo command reverts the deployment to the previous know state (v2 of the image). Updates are versioned and you can revert to any previously known state of a deployment.

Use the get pods commands to list the Pods again:

##### kubectl get pods

o/p

NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-7d44784b7c-96vjv   1/1     Running   0          45m
kubernetes-bootcamp-7d44784b7c-fk28v   1/1     Running   0          45m
kubernetes-bootcamp-7d44784b7c-hx5sz   1/1     Running   0          94s
kubernetes-bootcamp-7d44784b7c-sfjq2   1/1     Running   0          45m
$ 

Four Pods are running, To check the image deployed on these Pods, use the describe pods command:

##### kubectl describe pods

The deployment is once again using a stable version of the app (v2). The rollback was successful.

