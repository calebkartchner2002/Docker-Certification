# Docker Certification Training

Link to offical study Guide: https://training.mirantis.com/certification/dca-certification-exam/


## Notes as I Study
- A swarm service is a declaration that docker will maintain that there is some number of containers running at any time. Service is the **desired state (keep three replicas running)**
- A task is the unit of work Swarm creates to fulfill that desired state
- a container is what actually runs as a result of the task

- Global service: Swarm runs exactly one task per node, automatically — one on every node in the cluster.
    - you don't specify `--replicas` for a global service
- replicated service: Swarm creates exactly the number of replicas you specify, and it can place multiple containers on the same node if needed.

- Routing Mesh (cluster-wide load balancing): handles ingress traffic
    - Ingress traffic refers to the flow of data entering a network from external sources
- If you publish a port on a service, every node listens on that port, even workers without containers.
- Any node that receives the traffic will route it to a node running a task.
    - This uses IPVS (Layer 4 load balancing) under the hood.
- VIP(Virutal IP) Load Balancing (internal service load balancing): 
    - Every service gets a VIP (virtual IP).
    - Other services talk to the VIP, and Swarm load-balances traffic to tasks.
- An overlay network is a virtual network that spans multiple Swarm nodes, letting containers communicate as if they’re on the same LAN, even though they’re on different hosts.
- an ingress network will be created for any service that is given a `--publish`. for this service a routing mesh will be able to trip an Assigned ingress VIP and there will also be a port listener. For all services (and only this for internal services) in the cluster are given an overlay VIP for internal communication accross the cluster.

- orchestration ensures desired state is actual state. A Controller is used in the orchestration logic.
- Scheduler assigns tasks to worker nodes and is run by the Manager nodes.
- Manager is the host running the control plane
- Controller is part of the manager that enforces desired state
- Raft is a distributed key-value store that holds the entire cluster state of the Swarm.
- 
