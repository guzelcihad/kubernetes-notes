## Ingress Networking
Built in load balancer lives with Kubernetes.

<p>

The problem is that whenever we deploy a services in K8s, we need to configure load balancer
to route the request into this services, this is so much work, because each time we need a loadbalancer
and after time we need loadbalancer to route request to another load balancer. Instead
there is a built in loadbalancer called ingress in K8s.

![](../images/11.PNG)

Nginx is supported by Kubernetes. We simply deploy an Nginx Deployment 

### Network Policies
Allow rules based for POD. It is like security group in AWS

- Ingress Traffic: incoming request
- Egress Traffic: outgoing request 

# State Persistence
In docker, the data live within the container. If the containes dies then the data live within also.
To persist data we use volumes. Its same in the Kubernetes.
![](../images/13.PNG)

We can use storage solutions to work with Kubernetes. For ex: AWS EBS
This way volume storage will now be located in AWS.
