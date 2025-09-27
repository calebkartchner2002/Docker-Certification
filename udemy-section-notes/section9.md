# Section 9: Swarm Intro and Creating a 3-Node Swarm Cluster

## Swarm Mode: Build-in Orchestration
- Swarm Mode is a clustering solution build inside Docker
- docker swarm and related features are not enabled by default: swarm, node, service, stack, secret
- There are manager nodes inside a swarm that have their own RAFT database to give them the credentials they need inside a swarm
- every manager keeps a copy of the certificate authority and encyrpt their communications so that the top level is secure
- there are the worker nodes that take commands from the managers
- all nodes communicate over the "control plane"
- managers and become workers and vice versa. There is a demotion and promotion proccess that can be done.
- The managers have a shared internal distributed state store, This space where they all communicate including the encyrted messaging bewteen the managers is called the 'Raft concsensus group'
- The space where the workers communicate among themselves without the certificate authority is called the 'gossip network'
- There are range of functionalities within the RAFT manager nodes: API, Orchestrator, Allocator, Scheduler, Dispatcher

## Create Your First Service and Scale it Locally
- To enable docker sawrm: `docker swarm init`
    - in case using wsl, you will have to specify your prefered deployment network which you will want to be the `WSL network interface` (eth0)
    - this created a root signing cerftiicate created for our swarm
    - The certificate is issued for the first mamanger node
    - join tokens are created
- A Raft database is created upon `docker swarm init` which stores root CA, configs and secrets
    - Encrypted by default
    - This will be the only key/value system to hold orchestration secrets
    - Logs are replicated amongst managers via mutual TLS in 'control plane'
- Docker swarm can allow you to do a rolling blue-green update: No down time.
- Docker swarm automatically brings back up containers that fail or are brought down.
- When defining the creation of containers to an orchestration system, you are putting actions into a queue that build out the requests.
- by defualt running services shows resutls so if you want things done with automation use the `--detach true` tag.
- use Multipass as a better option for managing multiple local VM's

## Creating a 3-Node Swarm Cluster
- There are a couple options for creating multiple VM's: `play-with-docker.com`, Multipass, Digital Ocean
    - with Multipass you can directly ssh into VM's using firendy VM names
    - If you are provisioning a VM without docker, use `get-docker.com` to see easiest install methods, likely `curl`.
- Once the three VM's are created with docker installed, you can run `docker swarm init` which will provide you a link to join other nodes into its swarm.
    - You can specify whether needs nodes are going to be worker nodes or manager nodes.
    - update a node: `docker node update --role manager name_of_node`
    - to get the join token directly for a manager rather than a worker: `docker swarm join-token manager`
- you can use `docker service scale` or `docker service update <service name> --replicas #` to service multiple containers
