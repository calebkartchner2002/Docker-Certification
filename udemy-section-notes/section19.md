# Section 19: Kubernetes Management Techniques

## YAML Generators in kubectl Commands
- kubctl commands use helper templates called "generators". This includes apply, create, etc
- Every resource in Kubernetes has a specification or "spec"
- You can output templates with `--dry-run=client -o yaml`
    - These YAML defaults are good starting points for further customization
- Generators are "opinionated defaults", but in general these are small gerneralized rules which work for most use cases.
- Templates contain the layers of specs that define each resource policy and definitions that you can change to adjust the resource.
- EX: `kubectl create job test --image nginx --dry-run=client -o yaml`

## Imperitive vs Declaritive
- *Imperitive approach*: How a program operates
    - imperitive is an easier method when getting started and when you know the current state status
    - Is easier for humans at the CLI but not easy to automate
- *Decalrative approach*: What a program should accomplish
    - you can apply a state that you want: `kubectl apply -f my-resources.yaml
    - commands are very similar and can be automated fairly easily, except for `delete`
    - resources can be all in files or many files
    - much more work than `kubectl run` for just starting a pod
    - you must have good undestanding of YMAL keys and values

## Three Management Approaches
- *Imperitive Commands*: `run, expose, scale, edit, create deployment`
    - best for dev/learning/personal projects
    - easy to learn, hardest to manage over time
- *Imperative Objects*: `create -f file.yml`, `relace -f file.yml`, delete...
    - good for production of small enviorments, single file per command
    - store your changes in git-based yaml files
    - hard to automate
- *Declarative objects*: `apply -f file.yml or dir\, diff`
    - Best for prod, easier to automate
    - Harder to understand and predict changes

- IMPORTANT: Do not mix the three approaches
- Learn the imperative CLI for easy control but move to apply -f file.yml for prod.
- Store yaml in git, git ocmmit each change before you apply
- This trains you for later doing GitOps (git commits are automatically applied to clusters)
