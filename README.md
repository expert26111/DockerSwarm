**Deployment**: Why Docker Swarms is the way to go
----------------------------------------------

### <img src="https://upload.wikimedia.org/wikipedia/commons/a/a2/Simpleicons_Business_pencil.svg" width="18" height="18" /> Written by Yoana Dandarova & Manish Shrestha

 <img src="http://simpleicon.com/wp-content/uploads/Calendar-1.png" width="18" height="18" /> 10.12.2017
 


----------

<p align="center">
  <img src="http://everett-toews.github.io/effective-docker-swarm/slides/img/swarmnado.gif" alt="Docker Swarm"/>
</p>

## Table of Contents

1. [Abstract](https://github.com/expert26111/DockerSwarm#abstract)
2. [Problem Statement](https://github.com/expert26111/DockerSwarm#problem-statement)
3. [Docker Swarms](https://github.com/expert26111/DockerSwarm#docker-swarms)
4. [How Docker Swarms helped us](https://github.com/expert26111/DockerSwarm#how-docker-swarms-helped-us)
5. [Previous Works](https://github.com/expert26111/DockerSwarm#previous-works5)
6. [Future Works](https://github.com/expert26111/DockerSwarm#future-works)
7. [Conclusion](https://github.com/expert26111/DockerSwarm#conclusion)
8. [Technical Implementation](https://github.com/expert26111/DockerSwarm#-technical-implementation)
9. [Sources](https://github.com/expert26111/DockerSwarm#sources)
 
### **1. Abstract**
A system is bound to experience an outage, whether by its inability to handle multiple, concurrent requests or even after important updates.

Companies cannot afford to have their system down and inaccessible.

Docker Swarm allows to create multiple replicas of a primary instance of a system, providing redundancy and enabling Docker Swarm failover if one or more nodes experience an outage.

This implementation brings in scalability and automatic load balancing by nature, resulting in not only continuously alive system but also with increase in security and a better overall deployment approach.
 
### **2. Problem Statement**
Nowadays, every system, no matter how big it is, sooner or later reaches the point to be deployed, making it accessible from everywhere. There are a few in a bunch whose application fulfills the people’s requirements and goes viral, leading to it getting requests by millions of concurrent users. Many times, developers are not well-prepared to handle this amount of traffic which results to the hosted system to die down, until properly configured to scale. However, this problem can persist with large corporations as well, and this is not something they can afford to keep facing. Consistent failure of a system leads to customers searching for other solutions.
 
 
### **3. Docker Swarms**
To battle that very problem, Docker Swarms was introduced on mid-2016 with Docker version `1.12.0`. Yes, there are other alternatives like Kubernetes and Marathon on Apache Mesos but Docker Swarms has relatively easier implementation and if already working with containers, it requires barely any changes in the already set up configuration file, if any.


*Docker Swarm gives elegant solution to the question “How should I deploy my system?”.*

Docker Swarm enables to orchestrate cluster of containers in order to have stable working hosted solution. It assigns containers to underlying nodes and optimizes resources by automatically scheduling container workloads to run on the most appropriate host. This Docker orchestration balances containerized system workloads, ensuring containers are launched on the host machine with adequate resources, while maintaining necessary performance levels. Since there are multiple instances of the container running concurrently, in an unfortunate event of some of them failing, it doesn’t result in the system being down.
 
With the use of Docker Swarm, manager nodes and worker nodes can be spawned and assigned with one or more tasks with the number of replicas of the said tasks. The manager nodes automatically spreads and balances the tasks among all of its workers and itself, resulting in an efficient system, making most use of its available resources.

![enter image description here](https://blog.jayway.com/wp-content/uploads/2016/06/swarm-architecture.png)

Moving forward, Docker Swarm comes with great security out of the box. When a node joins the swarm, it uses a token that not only verifies itself but also verifies it is joining the swarm you think it is. From then on, all communication between nodes take place with mutual TLS authentication.

The following are the complete list of features that comes with Docker Swarm.<sup>[**[1]**](#source1)</sup>

- Cluster management integrated with Docker Engine
- Decentralized design
- Declarative service model
- Scaling
- Desired state reconciliation
- Multi-host networking
- Service discovery
- Load balancing
- Secure by default
- Rolling updates

### **4. How Docker Swarms Helped us**

Whenever we worked with docker containers, on every update of the system or crash we had to manually run the following commands (depending upon if we wanted to tweak the system):

```
docker-compose down
docker-compose up -d
```

OR

```
docker-compose restart
```

After the above commands, sometimes we had to remove the cached latest image of certain container, in order for new one to be pulled, even though we specified the tag `:latest` for it.  We also had to stop the system in order to update it. During this period, the user requests were not accepted by the system.

Docker-swarms does not stop your system that is up and running. It updates the system in segments. As we have already discussed, docker swarms replicates the service and distributes it between its nodes. So, it runs the "old, not updated" system in a few of the nodes while simultaneously updating on the other nodes. Once that is completed, the updated system becomes active and the "old" system gets updated. This way, everything is still accessible for users.

It proved itself useful with load balancing as well. When we started only with containers, occasionally system went down and it was because of the load. When that happened after swarms was implemented, only a few "replicas" would be down and the other "replicas" would still be active and working. 

The following screenshot from the simulator's chart, accessed via `http://138.68.91.198/chart.svg`,  explains this better. 
>Note: This is before docker swarms was implemented.

![enter image description here](https://lh3.googleusercontent.com/YQPp9qMhEiG3nUhpKWqYG6Rs00sVS85rpnlN-jed2B7m-sPWB5a8BteQSBlQKpljzM3Cgz5pEv-uzQ=s350 "ourlatest.png")

The blue line shows the increment of post's `hannest_id` ingested by our system over-time. As shown, there's a thin, vertical line in the middle. This is exactly the scenario when the system was down due to it crashing during the middle of the night, when developers were not actively monitoring it. The next morning, the system was manually restarted and docker-swarms was implemented, later that day. The same can be seen from Kibana's dashboard below.
![enter image description here](https://lh3.googleusercontent.com/s7fhE-AfWSkMOBuQVjSihjzmG6uVtOICNfjpwHtSQEB1a_6WShcIFvjKbnlr9vwdIKF0GiOsDaL1Kg=s0 "kibana.png")

The beginning of the graph is empty as the system was down due to overload.



### **5. Previous Works**<sup>[[5]](#source5)</sup>
As stated earlier, there are alternatives to implementation of container orchestration. Kubernetes and Marathon on Apache Mesos are the most actively used alongside Docker Swarms.

**Kubernetes**

This implementation is the widely adopted among all other container orchestrators. Founded by Google, currently under Linux Foundation and backed by industry giants such as IBM and Red Hat, it captures the majority of the market. It is targeted towards medium to large sized enterprises.

**Apache Mesos**

This also is targeted towards mostly large scaled companies, backed by Microsoft, Hewlett Packard Enterprise, Cisco, Dell, and many more.

**Docker Swarms**

Docker Swarms is another big player in the game and this is targeted towards small to medium sized enterprises. It is lacking the market share of Kubernetes although it has strong enterprise support. It is also the simplest implementation and since many developers are familiar with Docker containers, Swarms is the natural way to evolve into.

### **6. Future Works**

We have and will be implementing Docker Swarms with our future projects with special regards to networking, load-balancing and scaling. Our current project relating to Blockchain requires a peer-to-peer networking will be completely dependent upon docker swarms. 

We might also look into its alternatives but that has not been decided as of yet.

### **7. Conclusion**
 
So up until now, a conclusion can be made that docker-swarms is easy to manage with docker CLI, no need of additional software to create or manage a swarm. Ingress default network takes care of the load balancing. The option for rolling updates helps one update tasks images on all nodes where service is running.  The connection between different nodes is secure. Scaling is another option where docker-swarms shines, easy to implement with docker cli.

So one can see that using docker-swarms is easy to use and can helps not only with deployment but managing all problems associated with it in an efficient manner. There are many options for customizing not mentioned in this blog but docker-swarms is good decision for hosting your system.
 
----------



### <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/6f/Octicons-terminal.svg/1000px-Octicons-terminal.svg.png" width="20" height="20" /> **8. Technical Implementation**

If one want to be more orientated and get general help, can always start with the execution of `docker swarm --help`.

The command line tools for creating docker swarm cluster are easy to use.

![enter image description here](https://lh3.googleusercontent.com/wqWmz3r1PMjXpy1GM4ObYf40kNv4H2xdjdSXH5__Zo4rWnDFMTtE_eAxh0ZBmjRox3u0blUiFZN2aQ=s0 "swarm.png")

The following are the steps to create docker swarm and deploy services onto it.<sup>[**[4]**](#source4)</sup>

**1.** `docker swarm init --advertise-addr ipaddress`  *//Command 1*

With this command executed there is a node created. A node could be a DigitalOcean droplet, Virtual machine, etc.  Now what got created is a Manager node.

A Manager node, as every manager has someone working for them. So next, we create the worker nodes that will get managed by the manager node. 

After the execution of Command 1. One is presented with a token(a fairly long string of characters). In order to add worker nodes to your manager keep that token. 

**2.** `docker info`  *//Command 2*

After executing *Command 2* one sees: `Swarm active`

So it was so easy to create your manager, the one that will orchestrate your docker containers(also known as tasks).  Let's join another node to the swarm.<sup>[**[4]**](#source4)</sup>

**3.** `docker swarm join  --token “token goes here” ipaddress` *//Command 3*

After Command 3 one have joined a worker node to the cluster.

Because to spin a container is only possible from a manager. So in order to do that:

1. Go back to the manager node.
2. ssh into it.  (If you do not know how and what this means google on the internet). 
3. `docker node ls ` *//Command 4* (This command shows all nodes, workers and manager and their status.)

**4.** `docker service create -p 80:80 --name webserver nginx`  *//Command 5*

Command 5 is where there is a service created. If one already have a minimum knowledge of docker definitely appreciates how easy it is to create a service. And what is a service?  It is a container, task which is spun and deployed. 
 
**5.** `docker service ls` *//Command 6*

> Execute *Command 6* from the manager node(because worker nodes do not know anything about services).
 
After *Command 5* and *Command 6* on the console could be seen that the service is running as one replicas, the image `nginx`, how one named the service. Here, replicas is the number of containers for this service. In order to scale and run the service with multiple replicas use:

**6.** `docker service scale webserver=5`      *//Command 7* 

`docker service ps webserver`      *//Command 8*

After *Command 7 and  8*, one can see on the console on which node how many of the tasks/containers run. Here is where the magic starts to happen in favoring one’s hosted system. If a service goes down, it got restarted automatically on the same node or on different if the node no longer available. Here can be asked: "Okay I created my service and it got spun on multiple nodes, which IP address I provide to my users?" The answer is: It does not matter. The ingress network, on which by default the services got spun, takes care of routing the request to an active container and it does not matter on which host/IP address it is. With this in mind ingress network takes care of the distributing the load appropriately among workers/managers running the same service. Yes ingress does round robin by default for one’s system.<sup>[**[2]**](#source2)</sup>

**7.** How to update a service with the docker swarms? Okay, let’s do that. <sup>[**[3]**](#source3)</sup>

First step is to go to your manager node or one of them and ssh into it. 

`docker service create --replicas 3 --name redis --update-delay 10s redis:3.0.6` *//Command 9*

*Command 9* shows example how the rolling update policy can be configured at service deployment time.  The flag `--update-delay 10s` does this. One can use, for example 10m30s, time can be specified with seconds, minutes or hours. 

**8.** Updating a container image, is as well one line command. The swarm manager applies the update to all nodes. 

`docker service update --image redis:3.0.7 redis`  *//Command 10*

After *Command 10* what docker swarm does to update your task is:
stopping first task, schedule update for the stopped task, start the container and like this for all tasks in all nodes.

**9.** `docker service inspect --pretty redis` *//Command 11*

After *Command 11* the console prints for us the number of replicas the service has, the image and its tag, the update strategy one configured on the update of the service. If, for example, the update of a service(*Command 10*) failed, after *Command 11* in this case the state of a service will be marked as `Paused`. One can manually restart a service like :

`docker service update <SERVICE-ID>` *//Command 12* <sup>[**[3]**](#source3)</sup>

**10.** In order to escape *Command 12* and manually to restart a service one has to go back and configure her/his service again. To watch you service while updating:

`docker service ps redis` *//Command 13*

After *Command 13* one can see that some containers run image with different tag than others. This happens after *Command 10*. After all rolling updates are done one can see that all containers run the last updated version for her/his image.
 
What about when one  of the worker node crashes? Then docker swarm takes care of  moving the number of services your crashed node was executing to working instances. What about multi-host networking? No problem one can specify overlay network for her/his services on creation or update. So one not only can have the default ingress network but create her/his own. Okay, what about security, different nodes have different ip address and my server can be on different node than my database? How docker-swarms handle the secure connection between them? So each node in the swarm enforces TLS mutual authentication and encryption to secure communications between nodes.<sup>[**[1]**](#source1)</sup> 


----------


### **9. Sources:**
<sup>1</sup>  [Swarm Feature Highlights - Docker Documentation. *Published Date: unstated*](https://docs.docker.com/engine/swarm/#feature-highlights)<a name="source1"></a><br>
<sup>2</sup>  [Ingress Networking with Docker Swarm - Stack Overflow. *Published Date: Feb 28, 2017*](https://stackoverflow.com/questions/42510944/how-is-load-balancing-done-in-docker-swarm-mode)<a name="source2"></a><br>
<sup>3</sup>  [Applying rolling updates - Docker Documentation. *Published Date: unstated*](https://docs.docker.com/engine/swarm/swarm-tutorial/rolling-update/)<a name="source3"></a><br>
<sup>4</sup>  [Docker Swarm Tutorial - Toptal.com. *Published Date: unstated*](https://www.toptal.com/devops/software-deployment-docker-swarm-tutorial)<a name="source4"></a><br>
<sup>5</sup> [Docker Swarm Alternatives. *Published Date: July 7, 2017*](https://www.sdxcentral.com/articles/news/kubernetes-swarm-and-mesos-central-in-container-orchestration-space/2017/07/)<a name="source5"></a><br>
