# Section 17: Inspecting Kubernetes Resources

## Inspecting resources with Get
- The `kubectl get` command is very flexible, you can get Names such as `kubectl get deploy/my-apache`
- you can specify output: `-o` or `--output`. Here you can specify json, yaml, go templates and many more.
    - specifying --output as `wide` will provide containers. images and SELECTOR as additional pieces of information
    - SELECTORS and labels work hand in hand to allow one resource to find its other resources
    - For example a deployment creates a replicaset in which the connection is build from SELECTORS and labels, ie metadata
- Using YAML output has kubernetes output everything that it knows about the resource
    - YAML provides much more information that is set on default
    - YAML can be used as a configuration kubernetes initialization
- `Strategy` specifies what update strategy you will use and configuration for limits on that method.
- `Spec` is where containers are listed
- `status` shows current situation of resource: healthy, what state of launching.

## Inspecting Resources with Describe
- `Kubectl get` only shows one resources at a time
- `kubectl describe` will provide information on related objects to that resouce and events logs for that resource
    - This can be used on pods, deployments, nodes and any other resource
    - Describe is generally used as a more in depth tool to see whether your resouces are healthy, memory capacity, architecture and many more specific information. Generally seen as a report on a resource.

## Watching Resources
- use the `-w` on a resource command and this will apply a watch to the command, similar to linux watch
    - however instead of running the command over and over again, you can see in real time as things change. This is not good for seeing what happens in real time.
    - this shows all events from the past from the api database
- you can use `--watch-only` to show only changes that occur after the time you run the command.

## Container logs in Kubernetes
- logs are stored by default on each node with the container runtime (dockerd or cryo, etc). kubernetes logs tells the api to then tell the kubelets on the node to start sending logs back to the api that can then be sent to you.
- centralized logging that is widely parsable generally must be done with a third-party (AWS cloudfront or similar)
- `kubectl logs deploy/my-apache`
    - assumes that your logs command is being run on a container. Cannot be used for a service or another type of resource.
    - the logs command can be intelligent and when given a deployment pod, it will search through the replicaset and then the containers in the pods to return those logs. This will only return a random one of the containers logs but can be simple enough when having only one container in pod.
- to get the logs of a specific container: `kubectl logs pod/my-apache-8888888888-88888 -c httpd`. container names can be found with describe command.
- `stern` is a common libary for finding and collecting logs from containers. Used if you are unable to use or a centralized logging service.