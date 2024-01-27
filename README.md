# Kubernetes

Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

# What is Kubernetes?

Kubernetes is a orchestration framework for Docker containers. It allows you to deploy your applications quickly and predictably, scale them as needed, and manage them through a simple and elegant interface.

Google open-sourced the Kubernetes project in 2014. Kubernetes builds upon a decade and a half of experience that Google has with running production workloads at scale, combined with best-of-breed ideas and practices from the community.

# Need for Kubernetes

Rise of Microservices cost increased use of container technology. Containers are lightweight, portable, self-contained, and can run on any machine with the Docker engine installed. Containers are also easy to scale horizontally, and are therefore a good fit for microservices architecture.
We have multiple containers running on multiple machines.
Now the problem is how to manage these containers. We need a tool which can manage these containers as managing them manually using scripts is not a good idea.
That's where container orchestration tools come into picture. They help us in managing these containers.

They guarantee some features like:

- High availability means that your application is up and running as expected. If a container goes down, it is replaced automatically with a new one.
- Scalability means that you can scale your application by adding more containers if needed.(loads)
- Disaster recovery means that you can recover from a disaster quickly and easily.
- Load balancing means that the network traffic is distributed across multiple containers.
- Zero downtime deployments means that you can deploy your application without any downtime.

# Kubernetes Components

## Cluster

A cluster is a set of machines, called nodes, that run containerized applications managed by Kubernetes. A cluster has at least one worker node and at least one master node.
Cluster is the highest level of abstraction in Kubernetes.

## Master

A master is a node with three components that run the cluster:

- kube-apiserver
- kube-controller-manager
- kube-scheduler

### kube-apiserver

The API server is a component of the Kubernetes control plane that exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane.

### kube-controller-manager

The controller manager is a component of the Kubernetes control plane that runs controllers. The controller manager is a daemon that embeds the core control loops shipped with Kubernetes.

### kube-scheduler

The scheduler is a component of the Kubernetes control plane that handles scheduling onto nodes in the cluster. The scheduler determines which nodes are valid placements for each pod in the work queue according to constraints and available resources.

## Worker

A worker node is a node that runs user containers and daemons managed by the Kubernetes control plane, such as kubelet, kube-proxy, and container runtime. A cluster has at least one worker node.

### kubelet

An agent that runs on each node in the cluster. It makes sure that containers are running in a pod.

### kube-proxy

kube-proxy is a network proxy that runs on each node in your cluster, implementing part of the Kubernetes Service concept.

### Container Runtime

A container runtime is a software that is responsible for running containers. Kubernetes supports several container runtimes: Docker, containerd, CRI-O, and any implementation of the Kubernetes CRI (Container Runtime Interface).

## Kubernetes Objects

Kubernetes objects are persistent entities in the Kubernetes system. Kubernetes uses these entities to represent the state of your cluster. Specifically, they can describe:

- What containerized applications are running (and on which nodes)
- The resources available to those applications
- The policies around how those applications behave, such as restart policies, upgrades, and fault-tolerance

The following are the most important Kubernetes objects, which we will cover in more detail in this section:

- Pod
- Service
- Volume
- ConfigMap
- Secret
- Ingress
- Namespace
- Deployment
- StatefulSet
- DaemonSet
- Job

## Node

A node is a worker machine in Kubernetes and may be either a virtual or a physical machine, depending on the cluster. Each node has the services necessary to run pods and is managed by the master components. The services on a node include the container runtime, kubelet and kube-proxy. See The Kubernetes Node section in the architecture design doc for details.

## Pod

Smallest unit of Kubernetes. Pods create an abstraction layer over the container. A pod can have one or more containers. All the containers in a pod share the same IP address, IPC, hostname, and other resources. Pods are used to run multiple instances of the same application. Pods are ephemeral in nature. They can be created, destroyed, and re-created at any time. Pods are created and managed by the Kubernetes API server.

The layer is created because we want to abstract the container, in future if we want to change the container we can do it easily.
So, we only interact with kubernetes layer.
Usually one pod has one container but it can have multiple containers as well.

Each pod has a unique IP address. This address is shared by all the containers in the pod. This address is accessible from other pods in the same node. This address is not accessible from outside the cluster.
Pods are ephemeral in nature means they can die anytime. So, in that case a new pod is recreated with a new IP address.

But communication between pods is not reliable as pods can die anytime. So, we need a reliable way to communicate between pods. That's where services come into picture.

## Service

Service is permanent IP address for a pod. It is a reliable way to communicate between pods.

Lifecycle of a service is different from a pod. Even if a pod dies, service is not affected. Service is created and managed by the Kubernetes API server.

Now I want my app to be accessible from browser. So I need a external service.
That's where ingress comes into picture.
What ingress does is it exposes the service to the outside world.

## Ingress

Ingress is a collection of rules that allow inbound connections to reach the cluster services.
It can be configured to give services externally-reachable URLs, load balance traffic, terminate SSL, offer name based virtual hosting etc.

Suppose you have a mongodb app, where would the url be?
It would be inside built app. But what if you want to change the url?
You would have to change the app and rebuild it.
That's where configmap comes into picture.

## ConfigMap

It is external configuration for your application. It is a key-value pair. It is created and managed by the Kubernetes API server.
It contains the configuration of your application. It is used to store non-confidential data in key-value pairs.
You basically connect it to your pod and then you can access the data from the pod.

Now if URL changes, you just have to change the configmap and you don't have to rebuild the app.

Note: ConfigMap is not used to store confidential data like passwords, API keys etc. For that we use secrets.

## Secret

It is like CongigMap but it is used to store confidential data like passwords, API keys etc. It is created and managed by the Kubernetes API server.
It is stored in base64 encoded format.

Note: It is not encrypted. It is just encoded. So, it is not secure. So, don't store confidential data in it.

## Volume

Suppose if you have a database and you want to store the data in a file. But if the pod dies, the file will be lost. So, we need a way to store the data permanently. That's where volume comes into picture.

Volume attaches a physical storage to the pod. So, even if the pod dies, the data will be stored in the physical storage.
Storage can be done in local storage or cloud storage(outside K8s cluster).

## Deployment

If my app pod dies,I have a downtime where user can't reach app. This is the distributed systems and computing, we are replicating the app in servers. So, if one pod dies, the other pod will be up and running. So, there will be no downtime.
But I wouldn't create pods manually. I would define blueprint of the pod and specify the number of replicas. The blueprint is called deployment.
Deployment is a layer of abstraction over pods. Our interaction is with deployment and not with pods.

We cannot replicate a DB using deployment. For that we use statefulset.
Because DB has a state and we need to maintain the state.
We need manage the pods. Like which pods are writing to the DB and which are reading from the DB.

## StatefulSet

Meant for stateful applications like DB. It is a layer of abstraction over pods. It keeps the read and write pods synchronized,avoiding conflicts and inconsistencies.

But deplying statefulset is not easy. So, we use DB outside the cluster.

## DaemonSet

It ensures that all nodes run a copy of a pod. It is used to run a daemon on every node. It is used to run monitoring agents, log collectors, etc.

## Job

Job is a layer of abstraction over pods. It is used to run a task to completion. It is used to run batch jobs like data migration, data backup, etc.

## Namespace

Namespace is a way to divide cluster resources between multiple users. It is a layer of abstraction over cluster. It is used to divide cluster resources between multiple users. It is used to create virtual clusters within a physical cluster. It is used to create different environments like dev, test, prod, etc.

# Kubernetes Architecture

## Node Architecture

Nodes are the workers that run applications. A node can be a physical machine or a virtual machine. Each node is managed by the master components and contains the services necessary to run pods.

They have multiple pods on it.
It has 3 processes running on it:

- Container runtime
- Kubelet (Scheduler) interacts with container runtime and creates pods assigning resources to them.
- Kube-proxy (Network) is a network proxy that forwards traffic to the right pod. It is intelligent enough to know which pod is running on which node.

## Master Architecture

Master is the brain of the cluster. It is responsible for managing the cluster. It is responsible for maintaining the desired state of the cluster. It is responsible for scaling the cluster. It is responsible for deploying new applications. It is responsible for rolling out updates. It is responsible for monitoring the cluster. It is responsible for managing the cluster resources. It is responsible for managing the cluster security.

It has 4 processes running on it:

- API Server -cluster gateway,gatekeepeer for all the requests coming to the cluster, one entry point for all the requests.
- Scheduler- responsible for scheduling the pods on the nodes.
- Controller Manager - responsible for maintaining the desired state of the cluster.
- etcd - key-value store, stores the state of the cluster, are cluster brain.

## Example Cluster Setup

In a very small cluster setup, you have 2 master nodes and 3 worker nodes. You have 3 pods running on the worker nodes. You have 2 services running on the worker nodes. You have 1 ingress running on the worker nodes. You have 1 configmap running on the worker nodes. You have 1 secret running on the worker nodes. You have 1 volume running on the worker nodes. You have 1 deployment running on the worker nodes. You have 1 statefulset running on the worker nodes. You have 1 daemonset running on the worker nodes. You have 1 job running on the worker nodes. You have 1 namespace running on the worker nodes.

How do you add new master/ worker nodes to the cluster?

- Get a new server
- Install kubelet, kube-proxy, container runtime on the server(worker node processes)
- Add to cluster

# What is Minikube?

MiniKube is a tool that makes it easy to run Kubernetes locally. MiniKube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day.Mainly used for development and testing purposes.

It has docker and kubectl installed in it.

I can use virtualbox or hyper-v as a VM driver.

# Installing Kubectl

## Prerequisites

- VirtualBox or Hyper-V

## Installing Kubectl

- Download the latest release with the help of website based on your OS:

```bash
https://kubernetes.io/releases/download/
```

# Installing Minikube

## Prerequisites

- VirtualBox or Hyper-V
- kubectl

## Installing Minikube

- Download the latest release with the help of website based on your OS:

```bash
https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/
```

# Kubectl Commands

## kubectl version

```bash
kubectl version
```

## kubectl cluster-info

```bash
kubectl cluster-info
```

## kubectl nodes

```bash
kubectl get nodes
```

## kubectl pods

```bash
kubectl get pods
```

## kubectl services

```bash
kubectl get services
```

## kubectl deployment

```bash
kubectl get deployment
```

## kubectl config view

```bash
kubectl config view
```

## kubectl replica set

```bash
kubectl get replicaset
```

## kubectl edit deployment

```bash
kubectl edit deployment myimagedep
```

## kubectl logs

```bash
kubectl logs mongo-7bb86cb9-d8h8m
```

## kubectl describe pod

```bash
kubectl describe pod mongo-7bb86cb9-d8h8m
```

## kubectl exec

```bash
kubectl exec -it mongo-7bb86cb9-d8h8m -- bin/bash
```

## kubectl delete deployment

```bash
kubectl delete deployment myimage
```

## kubectl apply

```bash
kubectl apply -f config-file.yaml
```

## kubectl create deployment

```bash
kubectl create deployment mongo --image=mongo
```

# Minikube Commands

## minikube start

```bash
minikube start
```

## minikube status

```bash
minikube status
```

## minikube stop

```bash
minikube stop
```

## minikube delete

```bash
minikube delete
```

## minikube dashboard

```bash
minikube dashboard
```

## minikube ip

```bash
minikube ip
```

## minikube service

```bash
minikube service myimage
```

## minikube service list

```bash
minikube service list
```

## minikube addons list

```bash
minikube addons list
```

## minikube addons enable

```bash
minikube addons enable dashboard
```

## minikube addons disable

```bash
minikube addons disable dashboard
```

## minikube addons open

```bash
minikube addons open dashboard
```
