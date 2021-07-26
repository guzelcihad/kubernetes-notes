Patterns are
- Ambassador
- Adapter
- Sidecar 

 All containers running on the same Pod share the same volume and network interface of the Pod

## Sidecar
A good example deploying a log agent with a web server and collect data. Service meshes use this pattern<br>
Web server logs stored in pods resources and another log container access this log data.

## Adapter
Suppose that you want to monitor your pods but all pods have data in different format but your
monitoring services accept one format. Adapter provides an interface for this purpose.
This way we transform data that prometheus accepts

## Ambassador
Proxying connections from application container to other services.

<p>

Let’s assume that your application needs to connect to a caching server like Redis. In the development environment, Redis is installed on your laptop and listening on the localhost. In other environments, you have more than one Redis instance for high availability. You need to configure your application and select a healthy node then connect to it. One possible solution to this case is to use an Ambassador container that proxies connections to the backend Redis servers. The Ambassador container listens on the localhost as it is sharing the same Pod as the application container. In our example, we use Twemproxy to handle connecting to the Redis instances. The scenario can be depicted in the following illustration: