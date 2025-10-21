# Section 15: Kubernetes Architecture and Install 

## Kubernetes Architecture Terminology
- Kubectl: Cli to configure ubernetes and manage apps
- Node: single server in the kubernetes cluster
- Kubelet: Kubernetes agent running on nodes
- Control Plane (master): Set of containers that manage the cluster - same as managers in swarm
    - includes api server, scheduler, controller manager, etcd
- inside each master, there is multiple containers that run the system
    - there is `etcd` which is storage that holds keys
    - `API` is how to master container talks and controls the cluster
    - scheduler container controler how and where your containers are deployed in what are called pods
    - controller manager: really just a loop that checks to make sure what you ask is what is happening
    - Core DNS: something to help setup a DNS system
- Kubelet and kube-proxy runs inside the nodes that keeps them running

## Kubernetes Architecture Install
- Many wasys to install: `Katacoda` for online usage, easy integration with docker desktop, MiniKube on Windows or MicroK8's on Linux
- Docker Desktop comes with CLI, credentials, hosted kubernetes container on linux system.

## Kubernetes Container Abstractions
- Pod: one or more container running together on one Node
    - Basic unit of deployment. Containers are always in pods
- Controller: For creating/updating pods and other objects
    - Many types of controllers which include Deployment, ReplicaSet, StatefulSet, DaemonSet, Job, CronJob, etc.
- Service: network endpoint to connect to a pod
- Namespace: Filtered group of objects in cluster
- There is also secrets, ConfigMaps that work similar to docker.
