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
kind : Refers to the type of object we are trying to create. For ex: Pod, service, replicaset, deployment
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
