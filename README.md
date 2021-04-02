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
Containers are encapsulated into an object PODs. A pod is a single instance of an application.
<br>
It is the smallest object we can create in kubernetes.
<br>
Whenever the load is increasing we create another POD not another container in a POD. 
POD have one-to-one relationship with the container. But we can create multiple container within a POD.
<br>
Sometimes it can make sense, for ex: a helper container maybe for some reason it needs to be able to communicate
within the container where they live in one pod.

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
