# Section 20: Moving to Declarative Kubernetes YAML

## Kubectl apply
- You want to focus on building a containerized system to be as integrated into devops as possible. This means always using declaritive objects
- you can create/update resources in a file: `kubectl apply -f myfile.yaml`.
    - the component after `-f` can be a whole directory or a URL that contains the YAML files.

## Kubernetes Configuration YAML
- Each file can have one or more manifests
- Each maifest descirbes an API object (depolyment, job, secret)
- Each manifest needs four parts (root key: values in the file)
    - `apiVersion:`
    - `kind:`
    - `metadata:`
    - `spec:`
- I will be using the `assingment8-k8s-yaml` folder for the work in this module.
    - the file get larger and larger as the scope of the manifest gets larger: `app.yml > deployment.yml > pod.yml`
    - The format is somewhat set, can be extended with CRDs (Third party extensions)
    - you can put multiple manifiests in a file serpating with a `---`

## Building Your YAML Files
- `Kind`: can be defined as anything defined in `kubectl api-resources`, EX: `Deployment`, `Pod`
    - some "Kinds" are identical except for their API group definitions due to old vs new versions.
- `apiVersion`: can be defined as anything defined in `kubectl api-versions`
- `Kind` and `apiVersion` are used together to define exactly what resource you are getting
- `metadata` name of the resource that you are creating
- `spec` is where the specifics of each resource is defined, this differs per resource

## Building Your YAML Spec
- listing all of the different keys each `Kind` supports: `kubectl explain services --recursive`
    - `kubectl explain services.spec` will drill down into the YAML of the keys and their types that are expected.
    - you can continue using the `.` notation to drill down even futher if needed, EX: `depoyment.spec.template.spec.volumes.nfs.server`
- docs: `kubernetes.io/docs/reference/#api-reference`
    - contains spec details, examples of manifests 

## Dry Runs and Diffs
- the `dry-run=server` command will ask the server what changes will be made if a command is run, the server will respond with what resources will be created or changed would be. 
- `kubectl diff -f app.yml` will compare the given file to the spec in the server that is running.

## Labels and Label Selectors
- labels are optional and go under the `metadata:` in your YAML
- labels are a simple list of `key: value` for identifying you resource later by selecting, grouping, or filtering for it.
- Common examples include `tier: frontend`, `app: api`, `env: prod`, `customer: acme.co`
- Annotations are meant to store complex, large, or non-identifying info
- filtering a get command with a label: `kubectl get pods -l app-nginx`
    - there is quite complex filtering techniques similar to regex, will check docs if needed
- many resources use `Label Selectors` to "link" resource dependencies
- On nodes you can use labels and selectors to control which pods go to which nodes
- `Taints` and  `Tolerations` can also be used to control node placement (much more advanced)
