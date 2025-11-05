# Section 21: Next Steps and Future of Kubernetes

## Storage in Kubernetes
-  `StatefulSets` is a new resource type, making Pods more sticky
    - Good for databases and other persistant storage solutions
    - Generally try and use db-as-a-service rather than hosting these yourself
- you can define a Volume in the YAML spec in kubernetes
- Volumes
    - Tied to lifecycle of a Pod
    - Any containers in a single Pod can share them
- PersistentVolumes
    - Created at the cluster level, outlives a Pod
    - Separates storage config from Pod using it
    - Multiple Pods can share these
- CSI (container storage Interface) are third party plugins which are the new way to connect to storage

## Ingress Controller
- No Service types work at OSI Layer 7 (HTTP)
- Optional Ingress controllers route outside connections based on hostname or URL using 3rd party proxies
- Not installed by default but official features and mechanisms are provided
    - Ingress is installed seperately and then you must tell relevant Apps how to connect the Ingress
- Other options include Nginx, Traefik, HAProxy, F5, Envoy, Lstio, etc.

## CRD's and The Operator Pattern
- You can add 3rd party resources and controllers
    - This extends Kubernetes API and CLI
- Operator Pattern: Automate deployment and management of complex apps
    - EX: Databases, monitoring tools, backups, and custom ingresses

## Higher Deployment Abstractions
- All the `kubectl` commands just talk to the Kubernetes API
- Kubernetes has limited buil-in templating, versioning, tracking, and management of apps
    - There are 100s of 3rd party tools to implement this, many are defunct
- Helm is the most popular for deploying third party apps using "charts"
    - Controls and uses YAML sets that are deployed throughout your system.
    - Is the default for almost every deployment tool
- "Compose on Kubernetes" comes with Docker that also does this controlling of kubernetes
    - Allows docker formated YAML files to be used in Kubernetes (transforms it)
- Many of the deployment tools have templating options which are essential as enviorments/apps grow
    - `docker app` and `compose-on-kubernetes` is the Docker way for templates (uses CNAB standard, an agreement between Microsoft and Docker)

## Kubernetes Dashboard
- Commonly needed for multiservice systems so that people can view efficiently what is happening in the system as a whole
- There is a default GUI for "upstream" Kubernetes
- Generally GUIs including Kubernetes allows you to view resourecs and upload YAML
- The dashboard generally is the weakest link and can be hacked most easily. It is important to be aware of the wide range of capabilities and openness it provides to users who have access

## Namespaces and Context
- Namespaces limit scope (virtual clusteres)
- These are not related to Docker/Linux namespaces
- `kubectl get namespaces --all-namespaces`: shows all kubernetes api namespaces as well. 
- Three main parts of kubernests defined in `~/.kube/config`
    - Cluster
    - Authentication/User
    - Namespace
- context: defines how kubectl communicates with a specific cluster 
- `kubectl config set`: lets you update values in your Kubernetes kubeconfig file, such as the active context, cluster, user, or namespace

## Future of Kubernetes
- the goal is to be boring and to allow for developers to focus on their usecases of it. More focus on Stabilty, security
- More declarative-style features
- Better windows server support
- Kubernetes will continue to be the "differencing and scheduling engine backbone" for all new projects.

## Related Projects
- Knative: Serverless workloads on Kubernetes
- k3s: mini, simple kubernetes
- k3OS: Minimal Linux OS for k3s
- Service Mesh - New layer in distributed app traffic for better control, security, and monitoring
    - hoping to become the standard layer for increased developers ability to troubleshoot many microservices accross many servers.