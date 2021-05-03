When we install kubernetes on a system we actually installing following components.

* Api Server -> acts as frontend to the Kubernetes. The users, management, devices, CLI, all talk within the Api Server interact
with Kubernetes cluster.
* Scheduler -> Distributes the work on the containers across multiple nodes.
* etcd -> Distributed key value store to manage the cluster
* kubelet -> The agent runs on each node in the cluster. Making sure that the containers are running on the nodes as expected.
* Controller -> Brain behind the cluster. Responsible for noticing and responding when nodes, container goes down.
Make decisions to bring up new containers
* Container Runtime -> Underlying software that is used to run containers. It can be Docker, rkt, cri-o

Kubelet and container runtime are located in worker nodes the others ; scheduler, etcd, controller, api server are located in master nodes.

kubectl -> kube control is the command line tool. It is used to deploy and manage applications on a cluster.

# Kubernetes Concepts
## PODS
Containers are encapsulated into an object PODs. A POD is a single instance of an application.
<br>
It is the smallest object we can create in kubernetes.
<br>
Whenever the load is increasing we create another POD not another container in a POD. 
POD have one-to-one relationship with the container. But we can create multiple container within a POD.
<br>
Sometimes it can make sense, for ex: a helper container maybe for some reason it needs to be able to communicate
within the container where they live in one POD.

Example POD definition file

```
apiVersion: v1
kind: Pod
metadata:
  name: postgres
  labels:
    tier: db-tier
spec:
  containers:
    - name: postgres
      image: postgres
      env:
        - name: POSTGRES_PASSWORD
          value: mysecretpassword
```

## YAML
Lets look at a little bit about YAML file.

Key-Value Pair
```
Fruit: Apple
Vegetable: Carrot
Meat: Chicken
```

Arrays/Lists
```
Fruits:
-   Orange
-   Apple
-   Banana
```
Dictionary/Map
```
Banana:
    Calories: 105
    Fat: 0.04g
    Carbs: 27 g
```

List are ordere, but Dictionary is not.

## Concepts in Depth
Kubernetes has yaml files to manage components.
<br>
All yaml files includes those 4 top level properties.
```
apiVersion:
kind:
metadata:
spec:
```

apiVersion can have the following values for spesific compoenents:
Kind | Version
--- | --- |
POD | v1 | 283 |
Service | v1 | 283 |
ReplicaSet | apps/v1 | 283 |
Deployment | apps/v1 | 283 |

apiVersion : This is the version of the Kubernetes API you're using to create the objects.
<br>
kind : Refers to the type of object we are trying to create. For ex: POD, service, replicaset, deployment
<br>
metadata: Information about the object like its name, labels etc. This is form of a dictionary.
<br>
spec: Additonal information field. For ex: containers

## Replication Controller
* It ensures that specified number of PODs are running at all times. It provides HA.
* Another thing that it provides us is to create multiple PODs to share the load. Load Balancing and Scaling
* Replication Controller and Replica Set can be comparable. They do same purposes but Replication Controller is legacy 
and recommended to use Replica Set.
* There is a difference when defining a sample yaml file for both of them which is labels and selectors. This option is 
available in replica set. We just label PODs that we create and in the selector section provide this label name.
This way replica set know which PODs to monitor.

Example ReplicaSet File
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: mywebsite
    tier: frontend
spec:
  replicas: 4
  template:
    metadata:
      name: myapp-POD
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
  selector:
    matchLabels:
      app: myapp
```
### Labels and Selectors
Replication Controller monitors the PODs to decide its desired actions.
There are lots of PODs exist in the cluster so how Replication Controller understands
the which PODs to monitor?
<br>
This is where Labels and Selectors come to the rescue.
When defining a POD definition, we use label and then use this label in the Replication Controller
definition file. This is the same behaviour almost in all other objects in Kubernetes.
<br>
Here is an example:

pod-definition.yml
```
metadata:
  name: myapp-pod
  labels:
    tier: front-end
```

replicaset-definition.yml
```
selector:
  matchLabels:
    tier: front-end
```




## Deployments
* Provides deployment features like rolling updates.
* When we create a deployment, it creates replicaset and then replicaset creates PODs.

Example Deployment File, similar with ReplicaSet except kind field.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: mywebsite
    tier: frontend
spec:
  replicas: 4
  template:
    metadata:
      name: myapp-POD
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
  selector:
    matchLabels:
      app: myapp
```

### Rollout
When you first create a deployment, it triggers a rollout. A new rollout create a new deployment revision like revision1.
<br>
In the future when a container upgraded, a new rollout is triggered and a new deployment revision is created like revision2.
<br>
This helps us keep track of the changes made to our deployment and enables us to roll back to a previous revision.

#### Deployment Strategies
There are two types of strategies.
* The first one is to remove all PODs and create new ones. This strategy is called Recreate and it's not the default strategy.
* The second one is to remove one PODs then creates new one one by one. This is called Rolling Update. This is the default strategy.

* Under the hood, when we upgrade a newer version, Kubernetes creates a new ReplicaSet, creates one POD in new ReplicaSet
remove one POD in previous ReplicaSet. This way all the PODs are created in new ReplicaSet.
* When you want to rollback the rollout the opposite way of this arhchitecture work.

## Networking
* Ip address is assigned to a POD unlike docker. Docker assigned ip address to a container.
When Kubernetes initialized in a system, it creates a private internal network with the ip address 10.24.0.0.
When new PODs are created, they get assigned an ip address from this network. PODs can communicate each other through this ip.

When you first initialize a cluster, all the nodes creates this private internal network in their own.
But the problem is when you communicate PODs from one node to another node, then it is possible to have IP conflict.
<br>
Kubernetes doesn't provide a solution to solve this problem when you first initialize the cluster.
It expects from us to make it correct way.
<br>
The rules we need to follow:
* All containers/PODs can communicate to one another without NAT.
* All nodes can communicate with all containers and vice-versa without NAT.
It is our responsibility to setup the network in the correct way.

But there are some products to handle this problem like Cisco FCI, Vmware NSX, flannel, cilium.

## Services
Helps us to connect applications together with other applications or users.
For ex; front-end PODs to backend-PODs or users to front-end PODs.
Enables loose coupling between microservices in our application.

<br>
These are the services types:
* NodePort : Service objects listen to request coming to node in your cluster and then forward this request to the related POD.
* ClusterIp : Service objects create a virtual IP inside the cluster to enable communication between different services.
* Load Balancer : Provisions a load balancer for our application in supported cloud providers.
<br>

### Node Port
![](images/1.PNG)
There are 3 ports exist.
* First port in the image are called Target Port. This is exposed in the POD.
* Second port in the image are called Port. It forward request from Service to POD. Service object is like a virtual server inside
the node. Inside the cluster it has its own ip address and that ip address is called the cluster ip of the service.
* Third port in the image are called Node Port. This for accessing the web server externally. Node ports can only 
be in a valid range which by default is from 30000 to 32767.

### Cluster IP
As you know, PODs may go down and go up. So, in order to communicate PODs we need to define dynamic ip.
Cluster IP, provides a single interface for related PODs. 

![](images/2.PNG)

### Load Balancer
When we want users to interact with our applications we can use this type of Service object, to be able to
provide a single domain name. This service can be integrated with cloud providers.

## Namespaces
It is logical place where we keep resources. For ex: deployments, replicasets, pods
We can create namespaces for different environments for ex.
Kubernetes creates a namespace by default for its internal configurations.

For ex: if we create a pod
```
kubectl create -f pod-definition.yml
```
it exists under the default namespace.
To be able to create it under another namespaces, simply use
```
kubectl create -f pod-definition.yml --namespace=dev
```
Or simply use namespace in yml file under metadata section.

* Creating namespace
```
kubectl create namespace dev
```
or with a yml file.

The contents of the file is:
```
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

# Configuration
## ConfigMaps
We can define configs in yaml file. But ConfigMap allows us to define configs in one place.
We first define ConfigMaps then inject it into PODs.
# Definition
There are two ways to define ConfigMap.
* Imperative way
```
kubectl create configmap
  <config-name> --from-literal=<key>=<value>
```
For ex:
Creates a config for 
APP_COLOR:blue 
APP_MODE: prod

```
kubectl create configmap \
  app-config --from-literal=APP_COLOR=blue \
             --from-literal=APP_MODE=prod
```
OR FROM A FILE
```
kubectl create configmap
  <config-name>  --from-file=<path-to-file>
```
For ex:
```
kubectl create configmap \
  app-config --from-file=app_config.properties
```

* Declarative way
```
kubectl create -f config-map.yaml
```
config-map.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
```

* View ConfigMaps
```
kubectl get configmaps
```
* Details
```
kubectl describe configmaps
```
# Injection
Under the spec section we simply create new tags.
For ex:
```
envFrom:
  - configMapRef:
      name: app-config  // name of the configmap
```
There are also another way of injecting, for ex: injecting only one config.

## Secrets
Similar to ConfigMaps, useful for sensitive data. For ex: storing db host, db user, db password.
First create a secret then inject into pod definition.
* Imperative way
```
kubectl create secret generic
  <secret-name> --from-literal=<key>=<value>
```

```
kubectl create secret generic \
  app-secret --from-literal=APP_COLOR=blue \
             --from-literal=APP_MODE=prod
```
OR FROM A FILE
```
kubectl create secret generic
  <secret-name>  --from-file=<path-to-file>
```
* Declarative way
```
kubectl create -f secret-data.yaml
```
secret-data.yaml
```
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  DB_HOST: mysql
  DB_Password: pass
```

* View ConfigMaps
```
kubectl get secrets
```
* Details
```
kubectl describe secrets
```
# Injection
Under the spec section we simply create new tags.
For ex:
```
envFrom:
  - secretRef:
      name: app-secret  // name of the secret
```