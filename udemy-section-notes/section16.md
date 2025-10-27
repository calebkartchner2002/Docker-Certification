# Section 16: Your First Pods

## Kubectl run, create, and apply
- `run` is closest to docker run but is being designed to only run pods.
- `create` is similar to swarm create
- `apply` is similar to stack in swarm

## Creating a Pod with kubectl
- `kubectl version`
- create simple nginx pod: `kubectl run my-nginx --image nginx`
- you can deploy a pod via YAML
- `kubectl get pods`, `get` is how you get resource types which can include includes pods. you can also get secrets, configs, networks, etc.
- `kubecrtl get all` gets all "common" things a develper needs such as the kubernetes service that is being run and related pods. Note that this is only for the current namespace.
- A pod is an abstraction that includes many containers that run on the same IP. A pod will deploy together and be able to access eachother over localhost.
- Kubelet is what tells the container runtime (cron-d, cryos, docker, etc) to create containers for you
- Kubernetes only creates pods, the kubelet is what tells the container runtime what containers to start up.
- user request --> control plane --> kublet takes creation command off queue --> containe runtime creates container
- Kubernetes quick reference guide: https://kubernetes.io/docs/reference/kubectl/quick-reference/

## Your First Deployment With Kubectl create
- `kubecrtl create deployment my-nginx --image nginx`, this rolls out the container in a production setting. There can be no overlapping container names.
    - The output will confirm that the creation was sent to the kubernetes API but not that anything has yet been created or is running correctly.
- Flow of deployment command
    1. deployment command is sent to Kubernetes API
    2. input is then stored in etcd 
    3. controller manager sees resource call to API and creates a replica set
    4. Scheduler will assign the the pods a node.
- Each version of a resource type has a replica set created so that even if something is being taken down or updated, the old version can still run. This is all done by the controller manager.
- The pods are created by the replica- set. You can see correlation in the naming of the pod with the random ID code put in the pod name from the replica-set name.
- repalce `create` and `run` for deletion of pods and deployments with `delete`

## Scaling ReplicaSets
- Apache will be used for future many examples: `kubectl create deployment my-apache --image httpd`, 
    - the demon file in binary that runs in the apache server is known as httpd.
- scaling up replicas of a deployment: `kubectl scale deploy my-apache --replicas 2`
    - commands between resource names and resource type (deploy and `my-apache`) can be seperated by space or `/` 
1. Kubectl scale will *change* the deployment/my-apache record
2. The Controller manager will see that *only* replica count has changed.
3. number of pods in replicaSet will change
4. scheduler sees a new pod is requested and will assign a new node
5. Kubelet see a new pod and will tell the container runtime to start httpd
- you can use `--command -- ping 1.1.1.1` to specify a command to be run in the pod creation or a deployment. 
