+++
title = "My take on Kubernetes: how and why?"
date = "2019-07-12"
author = "Rafael Moreira"
cover = "kubernetes.png"
images = ["posts/kubernetes-how-and-why/kubernetes_og.png"]
tags = [ "kubernetes", "k8s", "containerization" ]
description = "See what is my opinion on Kubernetes and how I share my experience on why you should use it and how you should use it."
disableToc = false
+++

## A bit of history

[Kubernetes](https://kubernetes.io/), commonly known as K8s, "is an open-source system for automating deployment, scaling, and management of containerized applications". Quotes aside, Kubernetes was release on 2014 by Google, as a free and opensource solution for orchestration on the cloud.

At the time, containerization was not something strange, since [Docker](https://www.docker.com/) had been around for 1 year, more or less. Docker brought out Docker-Swarm, a technology that was simple in it's concept: bring multiple machines together under one big roof, and have them work together, sharing resources and delivering computing to end-users.

On the other hand, Red Hat was offering a solution called [Openshift](https://www.redhat.com/en/technologies/cloud-computing/openshift), an also open-source and managed Kubernetes solution for enterprise-grade workloads. As you can see, at the time, containerization on the cloud was growing.

## Why containerization? And why not virtualization?

The main idea behind containerization is the ability to isolate workloads inside a system, making them independent from the host it is running on. This is important because it allows for easier management of services and improves security (more on this later).

The biggest alternative for containerization is virtualization. Whilst in containerization, usually, the engine runs on the host and shares its kernel for modules and basic Linux functionalities, in Virtualization each instance emulates its own Kernel, using the virtualization technology referring to each CPU architecture (AMD or Intel, for example).

Alibaba Cloud has a very succinct and simple post on Containerization vs Virtualization, you can see it here: [What Are the Differences Between Containers and VMs?](https://www.alibabacloud.com/knowledge/difference-between-container-and-virtual-machine).

In a nutshell, containerization allows you to port your application to any system capable of running the containerization engine in question, since you build an image that is run in a container. Each image has multiple layers that define the configuration of the final image and will result on your service/application.

So is containerization better then virtualization? Yes and no. Until this day, virtualization has advantages over containerization since, for instance, virtualization emulates a kernel and that comes with advantages for security, but containerization is faster, lighter and easier to manage.

Needing an hypervisor is also a downside to virtualization, since using Docker Engine will save you RAM and instantiate your containers way faster. Ever noticed how, when you boot up your Linux desktop there are a bunch of messages on the screen (may differ from one distribution to another)? Those are _pre-flight_ checks and service startups. Docker does not need that for each container, so the start process is simpler and faster.

## Why Kubernetes?

After Docker, we had containers. Each container was a service. But now what?

The main idea behind using multiple commonly called "servers" is simple: sharing the burden of the workloads and making sure your service is always available in the event of a failure on one of the servers.

But this was not enough. Sure you could design your cloud infrastructure in such a way that multiple servers communicated between each other and shared information about load status, health status in a more traditional way with the [LEMP Stack](https://lemp.io/), the [XAMPP Stack](https://www.apachefriends.org/) or any other stack you desired. For accessing multiple servers and perform updates on your software, service or maintenance on the OS itself you could use [Ansible](https://www.ansible.com/), for example (these are just some examples, there is an World of custom and very intelligent solutions to explore).

This is where Kubernetes reigns. Now that we know that we can have fast and light services running in each OS, in multiple containers, we want to orchestrate them, control them and maintain them. K8s does exactly that in a very customizable way.

Kubernetes allows you to [bring your own container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/) and if the options are not enough, just invent one and make it compatible, it's open-source so you can go hardcore with it and the [`kubelet`](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) will handle the management of your pods, their containers and the basic k8s services (that run themselves in pods as well, in each node, incredible right?).

K8s also has one very important aspect: it connects tightly to the Operating System it is running on. There is no surprise that the main Linux Distributions run some sort of version of the Linux Kernel and that every version that exists out there shares basic functions because that's how and why Linux works. K8s takes advantage of these aspects and let's you control aspects such as: runtime users (GID and GUID), RAM requests and limits for each container, privilege escalation (this one is very powerful and prevents so much damage in case of an attack), amongst an extensive list of possible configurations.

So, the main idea with Kubernetes is: bring all the children under one roof. You define the container specification, K8s will ensure they are healthy, running and that they don't misbehave, no matter how many nodes (aka "servers") you have, giving you important information about the node itself in the process (RAM, Disk Usage, etc).

## Kubernetes is Cloud-Vendor agnostic, but there's a catch...

K8s was designed to be Cloud Vendor agnostic. This means that a volume is a volume, no matter where you are: AWS, Azure, Google Cloud, etc. This means that the core functionalities should be the same wherever you are running your workloads on.

In fact, you can run Kubernetes on-premises and there are a lot of tools that allow you to scale your Kubernetes Clusters up and down as you wish, in a _bring your own hardware_ kind of party. The aforementioned tool Ansible allows for [some solutions](https://kubernetes.io/blog/2019/03/15/kubernetes-setup-using-ansible-and-vagrant/), but you have other solutions such as [Kubespray](https://github.com/kubernetes-sigs/kubespray) or [Talos Linux](https://www.talos.dev/)(one of my favorite projects so far regarding K8s, incredible work has been done by the Talos Team).

But there is a catch with this: how hard it is to manage your Kubernetes Clusters. For instance, if you have a small data-center, say 20 servers, you would still have the need to run an hypervisor to provision virtual machines to run K8s nodes on (this is one aspect virtualization is still needed and will be for a lot of time). Why? Because current AMD Threadripper CPUs have 64 cores and you want to share those cores between multiple virtual machines. One machine using 64 physical cores will do you no good besides the _flex_ that comes with it (please note that current Hypervisors are really fast and efficient, and technology improves daily).

Cloud-Vendors take this aspect to their advantage and offer you managed solutions, such as [AWS's EKS](https://aws.amazon.com/eks/) or [Google Cloud's Kubernetes Engine](https://cloud.google.com/kubernetes-engine). These are services that offer you a seamless Kubernetes experience, while maintaining a tight integration with the other available services from that vendor.

On AWS, for example, you can have an [Application Load Balancer](https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.4/) as your service's [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) and this is very important because it reduces the hassle to configure the ALB mmanually on the console, which can be a real pain when you have multiple services. On AWS you also have [Network Load Balancers](https://docs.aws.amazon.com/eks/latest/userguide/network-load-balancing.html) that can be either internal or external and they act on Layer 4 inside your VPC.

These are just a few examples, from personal experience (EKS is an Universe ready to be explored) but the way you deploy these resources (your manifests) is the exact same, regardless of the Vendor. The cloud vendor needs to adapt to kubernetes and not the other way around. An ingress is an ingress. A service is a service. These do not change, sure you may have to include special annotations for the API to ingest and generate the correct cloud resource, but there is no much more to running Kubernetes on the Cloud.

One more import aspect of Cloud Computing thatI have been observing recently is that they have been reducing the gap between infrastructure and services/K8s. You can run [Kubernetes as a Service (KaaS)](https://www.aquasec.com/cloud-native-academy/kubernetes-101/kubernetes-as-a-service/) on multiple cloud vendors and some offer control over your infrastructure (for example, AWS allows you to create [Autoscaling Groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html) that represent _your servers_ and translate to your K8s nodes), so why do I say the gap is smaller? Simply because if you design your kubernetes manifests correctly I can guarantee you that each pod will translate to a node, and you will be able to control absolutely everything going on inside the cluster just by using manifests. No need to tinker with virtual machines, setup firewall/web application firewall rules, nothing. Just fire and forget (actually do not forget, DevOps remember? Keep an eye out for your stuff!), simple as that.

## So... How should you run K8s?

It will depend on your use case, as always (and sorry for being vague). Imagine you only want a simple Wordpress service with a small MySQL database. This will run you 2 Docker containers. Below you can see an example of a [`docker-compose.yml`](https://docs.docker.com/compose/) file for this case.

```YAML
version: "3"

services:
  wordpress:
    container_name: wordpress
    image: wordpress:6.0-php8.1-fpm
    volumes:
      - wordpress-data:/var/www/html
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: buda
      WORDPRESS_DB_PASSWORD: pest
      WORDPRESS_DB_NAME: wordpress
    deploy:
      resources:
        limits:
          cpus: "0.75"
          memory: 2048M

  db:
    container_name: mysql
    image: mysql:5.7
    volumes:
      - mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: milan
      MYSQL_DATABASE: wordpress
      MYSQL_USER: buda
      MYSQL_PASSWORD: pest
    deploy:
      resources:
        limits:
          cpus: "0.75"
          memory: 2304M

volumes:
  wordpress-data:
  mysql-data:
```

Now let's port this to kubernetes:

```YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  creationTimestamp: null
  labels:
    io.kompose.service: wordpress-data
  name: wordpress-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
status: {}
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  creationTimestamp: null
  labels:
    io.kompose.service: mysql-data
  name: mysql-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Mi
status: {}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    kompose.cmd: kompose convert
    kompose.version: 1.26.1 (HEAD)
  creationTimestamp: null
  labels:
    io.kompose.service: wordpress
  name: wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      io.kompose.service: wordpress
  strategy:
    type: Recreate
  template:
    metadata:
      annotations:
        kompose.cmd: kompose convert
        kompose.version: 1.26.1 (HEAD)
      creationTimestamp: null
      labels:
        io.kompose.service: wordpress
    spec:
      containers:
        - env:
            - name: WORDPRESS_DB_HOST
              value: mysql
            - name: WORDPRESS_DB_NAME
              value: wordpress
            - name: WORDPRESS_DB_PASSWORD
              value: pest
            - name: WORDPRESS_DB_USER
              value: buda
          image: wordpress:6.0-php8.1-fpm
          name: wordpress
          ports:
            - containerPort: 80
          resources:
            limits:
              cpu: 750m
              memory: "2147483648"
          volumeMounts:
            - mountPath: /var/www/html
              name: wordpress-data
      restartPolicy: Always
      volumes:
        - name: wordpress-data
          persistentVolumeClaim:
            claimName: wordpress-data
status: {}
---
apiVersion: v1
kind: Service
metadata:
  annotations:
    kompose.cmd: kompose convert
    kompose.version: 1.26.1 (HEAD)
  creationTimestamp: null
  labels:
    io.kompose.service: wordpress
  name: wordpress
spec:
  ports:
    - name: "8080"
      port: 8080
      targetPort: 80
  selector:
    io.kompose.service: wordpress
status:
  loadBalancer: {}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    kompose.cmd: kompose convert
    kompose.version: 1.26.1 (HEAD)
  creationTimestamp: null
  labels:
    io.kompose.service: mysql
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      io.kompose.service: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      annotations:
        kompose.cmd: kompose convert
        kompose.version: 1.26.1 (HEAD)
      creationTimestamp: null
      labels:
        io.kompose.service: mysql
    spec:
      containers:
        - env:
            - name: MYSQL_DATABASE
              value: wordpress
            - name: MYSQL_PASSWORD
              value: pest
            - name: MYSQL_ROOT_PASSWORD
              value: milan
            - name: MYSQL_USER
              value: buda
          image: mysql:5.7
          name: mysql
          resources:
            limits:
              cpu: 750m
              memory: "2415919104"
          volumeMounts:
            - mountPath: /var/lib/mysql
              name: mysql-data
      restartPolicy: Always
      volumes:
        - name: mysql-data
          persistentVolumeClaim:
            claimName: mysql-data
status: {}
```

Disclaimer: I used [Kompose](https://kompose.io/) to convert the `docker-compose.yml` file into their equivalent manifests for Kubernetes. Also please do not let the giant YAML manifests scare you, K8s is really cool once you get the hang of it.

See the difference? And this does not include some basic services and configurations that you need to have running, such as [CoreDNS](https://coredns.io/), for example. Both will have the same result, with the difference of how much of an hassle it is to setup.

On top of this manifest management, you will need to manage the cluster they are running on, keeping tabs (possible setting up monitoring) on the health of the services, amongst other problems.

Before anything else you will need to be confortable with containerization of your applications. Then you will start to get the feel for what is easily ran on Kubernetes and what is harder to run or makes no sense at all. Always remember that you need to look at these technologies from all angles, it's not just a _DevOps guy_ behind a service, there are developers or there are security teams or more people involved that need to also understand the basics of how the application will be ran and served.

So my final say is: if you are confortable with the learning curve, than Kubernetes won't bite you and will make you `future-proof` because, as I mentioned before, the current solutions bring the gap between infrastructure and Kubernetes smaller, so these deployment processes are becoming more and more simpler (and thanks to tools such as [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) or [FluxCD](https://fluxcd.io/) you can have your `infrastructure-kubernetes-ish` setup running automagically - I will make a more in-depth post regarding this topic).

Do not be afraid of KaaS, the cloud vendors design it to help you, not to be more difficult. There is no shame in using a production-ready failproof solution instead of a custom solution. If you need, installing Kubernetes manually can be a fun experience (more about this in the future) and will make you learn even more about the technology!

I recommend you use [Minikube](https://minikube.sigs.k8s.io/docs/start/) and start to experiment, get the feeling of Kubernetes and how it behaves, then you can start to dig deeper and lose yourself in it!
