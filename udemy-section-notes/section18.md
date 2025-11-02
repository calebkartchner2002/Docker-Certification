# Section 18: Exposing Kubernetes Ports

## Service Types
- a service is an endpoint that is consistent so that other things inside and outside the cluster can access it.
- by default a pod doesnt get a DNS name or IP address. Creating a service on top of a pod allow you to do that.
- `kubectl expose` allows you to open up ports
    - You have the option of a `ClusterIP`, this allocates a single internal virutal IP that is only reachable from within cluster.
    - You have the option of `NodePort`, this opens a port on every node's IP. Anyone can connect if they can reach the node.
        - pods do need to be updated to this port
    - ^ These are always available on kubernetes^
- there is a `LoadBalancer` which controls a LB endpoint external to the cluster
    - This is only available when a infrastructure provider gives a LB (AWS ELB)
    - This can create a Nodeport and ClusterIP services, you can tell LB to send to a NodePort
    - This is commonly used when having external traffic coming in.
- There is `ExternalName` which adds CNAME DNS record to CoreDNS only
    - Not used for pods but gives pods a DNS name to use for something outside kubernetes
    - This is used when trying to get information outside a cluster but when you don't have control of your DNS.
- There is also `Kubernetes Ingress` which is designed for http traffic
- Technically CoreDNS is not a required service in Kubernetes but you need it to resolve communication is any pod. Not included in MicroK8s

## Creating a ClusterIP Service
- `kubectl create deployment httpenv` is going to be used for sending simple test connections
- `kubectl expose deployment/httpenv --port 8888` this will create a cluster IP service in front of the deployment (not a seperate pod)
- `kubectl run tmp-shell --rm -it --image bretfisher/netshoot -- bash`
    - `curl httpenv:8888` this will return a response from the server
- running a temporary interactive container in a pod in kubernetes is very similar to docker as seen in the command above. Big differences is using `--` between options and command being run in container.

## Creating a NodePort and LoadBalancer Service
- Creating a Nodeport: `kubectl expose deployment/httpenv --port 8888 --name httpenv-np --type NodePort`
- The port range for NodePort is 30000-32767
- ClusterIP, NodePort and LoadBalancer are additive meaning that the ones later in this list will automatically create instances of the ones before it as they are dependent.
- Creating a LoadBalancer: `kubectl expose deployment/httpenv --port 8888 --name httpenv-lb --type LoadBalancer`
- any network packages that are passed to the loadbalancer is then passed to the nodeport which is then passed to the clusterIP.

## Kubernetes Services DNS
- Core DNS is the recommended kubernete DNS service. It is technically not a required service but one is shipped with almost every kubernetes setup.
- for pinging and using services, you are generally supposed to specifcy `<hostname>.<namespace>.svc.cluster.local`
- `default` is the namespace you will be working in my default without any specification - FQDN (fully qualified domain namespace)