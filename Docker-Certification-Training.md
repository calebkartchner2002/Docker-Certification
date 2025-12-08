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
    - All managers must agree before state changes (EX: create service)
    - One manager becomes the leader; others follow.
    - A majority of managers must be available to make decisions.
    - If a manager goes offline, cluster state is preserved as long as quorum remains.
- if a leader node dies, the managers vote for a node with the latest committed entires to become leader. This way the leader has the most updated True of the RAFT state
- all parts of the control plane are run from the Docker daemon: Raft store, orchestrator, Scheduler, Dispatcher, Allocator, Certificates subsystem (mutual TLS), API router
- Kubernetes is implemented using contaienrs such as a seperate API server container, seperate scheduler container, seperate controller-manager container

- secrets are encrypted in RAFT, configs are not
- Worker nodes that request a secret will decrypt RAM where it will be stored. 
- secrets ensure nothing is encrypted on disk, only ever on a worker node in memory

- `tmpfs` Lives only in memory, Disappears when the container stops, cannot persist and is best for sensitive data or very high I/O: `--mount type=tmpfs,target=/run/cache`
- Bind mounts do not automatically work in Swarm as The container could be scheduled on ANY node and that directory might not exist.
- Volumes are given a random name if not directly speciefied. difficult to reuse
- Volume = WHAT you're storing
- Volume Driver is how and where it's stored

- A `macvlan network` is where every container is assigned a mac address, similar to how a physical network works where each container would be a device on the network.
- `Host network` is where the docker network is the host's network, no seperation layer, no need to `--publish` ports.

- In dockerfiles:
- `ARG` is NOT available at runtime, it only exists during the image build.
- `ENV` persists into the running container, controls
- only these instructions create layers: `FROM`, `RUN`, `COPY`, `ADD`
-  Multi-stage builds let you use one image to build your application and a different, smaller image to run it. This drastically reduces final image sizes.
- Generally you should use `COPY` as it is simple and predictable. 
- ADD is for:
    - Extracting tar archives
    - Downloading URLs (downloading URLs is discouraged)
- `.dockerignore` dramatically speeds builds. You can reduce context size, build time and cache invalidation risk


## Things that I have been struggling with
- you can ensure session stickiness in docker swarm/compose/service by enabling `--publish mode=host,published=8080,target=80`. `host` mode makes so that a client will always connect to the same node IP and turns off routing mesh and load balancing
- Node availablity states:
    - `drain`: no tasks allowed. existing tasks get rescheduled to other nodes
    - `pause`: No new tasks but exisiting ones stay
    - `active`: can run tasks
    - command: `docker node update --availability drain NODE_NAME`
- Dependent startup in Docker Compose
    - `depends_on` tells docker compose in what order to start containers
    - healthcheck is the actual test the container has to pass to show its actual readiness
- multi-stage builds reduce image size by seperating build and runtime steps. For example runing NPM install during build process and then only coping the built files for the actual runtime image. 
- DTR (Docker Trusted Registry)
    - is park of Docker Enterprise, not community docker, enterprise version of Docker Hub
    - UCP (universal control plane): sits on top of docker engine like kubernetes control plane
    - DTR has multiple nodes for high availability: replication, shared storage, load balancing
- Notary provides signing for docker content.
    - docker content trust (DCT) uses Notary on the backend to verify and assign signatures
    - `DOCKER_CONTENT_TRUST=1` will require docker to verify signatures for pull and push, verify metadata via Notary server and allow storing of signing keys under `~/.docker/trust`
    - DCT relies on Root Key and Repository Key
- LDAP (Lightweight Directory Access Protocol)
    - a protocol to query and manage users in a directory
    - just a directory, does not provide RBAC, policies, roles, etc
- Active Directory is Microsoft enterprise LDAP system, essentially combines all athentication features
    - Stores users (using LDAP internally)
    - Authenticates users (Kerberos)
    - Authorizes users (groups)
    - Manages domain-wide security and policy
- SAML (Security Assertion Markup Language)
    - SAML is a protocol for Single Sign-On (SSO)

- `--endpoint-mode dnsrr`: this disables VIP per service and returns each task IP address directly via DNS
- The TCP/IP stack is the networking subsystem inside the operating system that handles pretty much all networking (all machines have one): opening ports, sending/receiving packets, establishing TCP connections, handling timeouts (retries), NAT, firewall rules, routing packets 
- iptables rules is the linux firewall: NAT, port forwarding, packet filtering, connection routing, DNAT (destination NAT) for Docker published ports
    - Docker automatically sets iptables rules
- reverse proxy: a service that accepts incoming client requests and forward them to backend services. EX: swarm ingress is a reverse proxy on every node
- IPVS (IP Virtual Server): It is a Layer 4 load balancer built into the Linux kernel
    - Docker Swarm uses IPVS to implement service VIPs
- DNS TTL = Domain Network Service Time to Live
- During VIP mode (default in swarm), when a TCP connection is made, state is stored in client and server kernels.
    - When a node dies using VIP, the task's TCP session state disappears with the node. once swarm removes the downed node from the backend IPVS, a new TCP connection will be made.
- Masquerading Rule: iptables rule that rewrites container src IP → host IP:
- NAT translates container’s private IP → host IP.
- volumes are stored in `/var/lib/docker/volumes`
- `seccomp` by default blocks syscalls. This includes low-level perf monitoring and kernel module loads.
- containers drop root capabilities by default: 
    - `NET_ADMIN` which is needed for network interfaces
    - `SYS_ADMIN` is pretty much root on host, very dangerous
    - `SYS_PTRACE` meant for debugging
    - done using `--cap-add=NET_ADMIN` or `--cap-drop=ALL`
- `docker login` writes credentials to `~/.docker/config.json`
- Update a service:
```sh
docker service update \
  --image myimage:v2 \
  --update-delay 10s \
  --update-parallelism 2 \
  myservice
```
- rollback an update: `docker service update --rollback myservice`

- A multi-stage build lets you use multiple `FROM` blocks in a single Dockerfile. Each `FROM` starts a completely new build environment:
```sh
FROM golang:1.22 AS builder`
WORKDIR /src
COPY . .
RUN go build -o app
FROM alpine:3.20
COPY --from=builder /src/app /app
```
- `docker system prune` removes: stopped containers, networks not used, build cache
    - adding `-a` removes all unused images (not just dangling)
    - `docker container prune` only removes stoped containers
    - `docker image prune` deletes dangling images, `-a` removes all unused images
    - `docker volume prune` delete volumes that re not attached to containers
    - `docker network prune` removes unused bridge networks

- Docker uses `overlay2` for it's container filesystems
    - *lowerdir*: read-only image data
    - *upperdir*: writable data (for a container)
    - *workdir*: scratch space for merging
    - *merged*: final union filesystem exposed to container 
- Garbage Collection in a registry removes unreferenced manifests, blobs not tied to tags and dangling layer data
    - to run you must first make the registry `read-only`
    - then run `registry garbage-collect /etc/docker/registry/config.yml` 
    - there is no TTL for images in Docker Registry, you generally have to manually delete them. Github container Registry and ECR have lifecycles built in.

## Indepth struggle notes
- Kubernetes
    - A persistent volume is an actual peice of stage that the cluster admin has provisioned, or can be dynamically provisioned
    - A persistent volume claim is a request for storage by a pod
    - PVC's and PV have properties:
        - `storage`: a PVC will need at least its requested storage or larger, any larger and that space will be "taken" but not used
        - `Access Mode`: ReadWriteOnce(RWO), ReadWriteMany (RWX), ReadOnlyMany (ROX)
        - `StorageClass`: defines the type of storage that will be used
        - PVCs cannot shrink, only expand (if `allowVolumeExpansion: true` is set in StorageClass)
            - exapnsion request is sent through PVC spec change 
            - editing the `spec.resources.requests.storage` can change the size
            - You increase the PVC's requested storage → the PV is marked for expansion → the storage backend enlarges the underlying block device → Kubernetes updates the PV's capacity → kubelet (or the CSI node plugin) resizes the filesystem on the node → the pod sees the new larger volume
        - 1:1, one PVC binds to exactly one PV.
    - if a pod creates a PVC and there is none available, dynamic provisioning may occur if
        - The PVC specifies a StorageClass
        - or The cluster has a default StorageClass
    - A `StorageClass` tells Kubernetes how to create a PV dynamically
        - The provisioner plugin (example: kubernetes.io/aws-ebs)
        - Default reclaim policy (often Delete)
            - Retain -> Do nothing. Admin must clean up manually
            - Delete -> Delete the backend storage (EBS volume, GCE PD, etc.). This is common in cloud setups
        - `volumeBindingMode` option:
            - `Immediate`: Provision PV as soon as PVC is created
            - `WaitForFirstConsumer`: PV isn’t provisioned until a Pod attempts to use it, this is great for cloud as it ensures the volume gets created in the right availability zone
    - PVs are cluster-level; PVCs are namespace-level 
    - PV lifecycle outlives Pods but NOT necessarily PVCs
    - when a PVC is created and references a `StorageClass`, The control plane contacts the provisioner (Container Storage Interface (CSI) is the modern and recommened one) to create a real disk in a cloud provider (EBS/GCE PD/Azure Disk/etc.)
    - `volumeMode: Filesystem` : Provisioner formats the disk (ext4/xfs/etc.) and Pod mounts it as a directory
    - `volumeMode: Block`: Raw block device is exposed directly to the container 
        - Not all CSI drivers support Block mode, used for databases that want to manage their own filesystem
    - Namespaces(which are completely different in kubernetes compared to linux namespaces) do not affect IP allocation
        - Every pod gets one IP. All containers in a pod share that single IP
- Linux Namspaces: provide kernel-level isolation:
     - PID, NET, MNT, UTS, IPC, USER
- Kubernetes namespaces are organizational boundaries, folders for your API objects
- Docker Model:
    Node
      └─ Container
      └─ Container
      └─ Container
- Kubernetes Model:
    Cluster
      └─ Node
          └─ Pod
              └─ Container
              └─ Container
          └─ Pod
              └─ Container
- kubernetes pods contain a group of container which all share the same IP, storage, and very intertwined whereas as every docker container is independent, has its own storage, own IP, PID

- A logging driver in Docker is the mechanism that decides where container logs go and how they’re stored/forwarded
    - common drivers: `json-file`(default), `local`, `syslog`, `awslogs`, `splunk`, `none`
    - You can configure the default logging drivers in every container (unless overriden) in `/etc/docker/daemon.json` 
    - you can set logging driver when running a container: `docker run -d --name myapp --log-driver=json-file`
    - or you can set the logging driver in the `docker-compose.yml` with `logging` underneath the named service
    - A logging driver routes container `stdout/stderr` to different backends
- In Kubernetes, Requests are made to the Universal Control Plane (UCP) API. 
    - UCP audit logging allows you to see past requests
    - to capture all HTTP actions (GET/POST/PUT/PATCH/DELETE) against the UCP API, Swarm API and Kubernetes API, enable: `metadata` level or  `request` level
        - In the UI: Admin Settings -> Logs & Audit Logs -> Configure Audit Log Level
    - Once enabled, the audit events are written into the logs of the `ucp-controller` container on each manager node
- UCP (Universal Control Plane) is Docker Enterprise’s central management and security control plane for container clusters
- If you use a custom CA or self-signed certificate, every Docker Engine, UCP node, and client pulling from DTR must trust that CA
    - you must copy your CA root certificate into: `/etc/docker/certs.d/dtr.example.com/ca.crt`
    - after putting `ca.crt` in place, you must `sudo systemctl restart docker`
- An insecure registry is a container registry that does NOT use HTTPS (TLS) or uses a self-signed certificate that Docker does NOT trust
    - to allow an insecure registry, add `"insecure-registries": ["myregistry:5000"]` to the `daemon.json`
- A registry mirror is a caching proxy for Docker Hub or another upstream registry
    - enable in `daemon.json` with `{"registry-mirrors": ["https://mirror.gcr.io"]}`
- `docker events` is a real-time event stream from the Docker daemon. Examples:
    - `--filter container=mycontainer`
    - `docker events --filter event=health_status`
- To encrypt the overlay network on docker, run `--opt encrypted` in you docker network create command
    - this enables IPSec encryption for VXLAN packets between swarm nodes
    - this encrypts data-in-transit only between nodes
    - encryption keys are rotated automatically by Swarm
- a docker stack is the way you deploy and run a full Swarm application using a docker compose style file
- `overlay` is the only docker network that can you have multiple hosts

## Next Struggle section
- joining a manager and worker node is almost the same except there is a unique token for joining as each: `docker swarm join --token <worker-token> <manager-ip>:2377`
    - you can join any manager in the swarm
- The UCP client bundle lets you talk securely to the UCP cluster with your identity and permissions.
- you can download the client bundle from UCP and get a ZIP file:
    - The UCP CA certificate
    - A client certificate and private key
    - This entire bnudle authenticates you as your UCP user and applies the RBAC permissions assigned to you in UCP.
    - a `.env.sh` script that sets enviorment variables
        - `DOCKER_HOST=tcp://ucp.example.com:443`
        - `DOCKER_TLS_VERIFY=1`
        - `DOCKER_CERT_PATH=/path/to/this/bundle`
- A restricted UCP user can view but cannot cahnge anything in the docker or kubernetes setup.
- Labels are key–value pairs attached to Kubernetes objects (pods, deployments, nodes, namespaces, etc.)
    - Set-based selectors: `kubectl get pods -l 'env in (development)'`
    - Equality-based selectors: `-l env=development` or `-l app!=frontend`
- DTR after completing security scans can send webhooks to other applications such as gitlab to other proccessing such as unit tests. 
- DTR Cache is a read-only registry replica used to speed up global image distribution.
    - user authentication is forwarded to the primary DTR
    - Acts like a virtual load balancer.
- kube-proxy is a node-level network proxy that maintains the rules that make Kubernetes Services work
    - Programs NAT, iptables, IPVS rules on each node
    - Provides load balancing between pods behind a Service
    - Watches the API server for: New Services, Deleted Services, Updated Endpoints (pods)
- ClusterIP is the internal virtual IP assigned to every Kubernetes Service
    - Only reachable inside the cluster.
    - Never tied to any pod or node.
    - kube-proxy ensures connections to the ClusterIP get NATed to pod IPs.
- overlay networking is applied via a CNI plugin (container network interface)
- one namespace can talk to another but specifying its cross-namespace form:
    - something in `dev` trying to reach a service called `api` in `test`, it must call `api.test` or `api.test.svc` or `api.test.svc.cluster.local`
- with `-p`, you specify the port mapping. With `-P`, Docker automatically publishes all EXPOSEd ports using random high host ports
- Priority order for docker daemon: CLI flags override, daemon.json, system/defaults

- you can set insecure registries in `/etc/docker/daemon.json` as "insecure-registries": ["host:port"]
- passing the `--insecure registry` flag to the daemon at run time is a way to configure the docker engine to use a registry without a trusted TLS certificate
