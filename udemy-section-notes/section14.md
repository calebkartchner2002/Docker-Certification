# Section 14: The What and Why of Kubernetes

## What is Kubernetes
- open source, manages (orchestrates) containers
- is a set of API's that run on containers that manage a set of servers and execute your containers in docker(or another similar platform)
- kubernetes run with similar structure to docker swarm. Biggest use case of kubernetes is from cloud vendors

## Wky Kubernetes
- big picture, it providers faster devops
- generaly servers + change Rate = benefit of orchestration
- Almost never use the pure upstream version of kubernetes, vendor solutions provide a lot more netowrking, security and faster deployments.

## Kubernetes or Swarm
- Generally Swarm is easier to deploy/manage while kubernetes provdes more features and flexibility
- Swarm Advantages:
    - Swarm comes with Docker, single vendor contaienr platform
    - easier deployment
    - follows 80/20 rule. 20% of features for 80% of use cases
    - Runs anywhere where docker does: local, cloud, datacenters, ARM (all editions), Windows, 32-bit, etc
    - Secure by default out of the box (mutual TLS authentication, encrypted databases and credentials)
    - Easier to troubleshoot
- Kubernetes Advantages:
    - Clouds will deploy/manage Kubernetes for you
    - infrastructure vendors are making their own distubutions: VMWare, NetApp, etc
    - Flexible: Widest adoption and support
    - "Kubernetes first" vendor support

## Side note: kubernete depricating docker shim
- docker used to be the default container platform used in kubnetes
- Kubernete maintainers decided this was ineffient and difficult to maintain:
    - Before the change to containerd: `kubelet → dockershim → Docker Engine (dockerd) → containerd → runc`
    - After: `kubelet → containerd → runc`
- Docker is still comonly used in development, smaller projects and Swarm but not by kubernetes.

