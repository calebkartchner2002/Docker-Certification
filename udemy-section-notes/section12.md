# Section 12: Container Registries: Image Storage and Distribution

## Docker Hub: Digging Deeper
- Docker Hub contains webhook capabilities to build CI/CD pipelines to deploy updated containers accross platforms
- you can also link github/gitbucket so when code is updated there is automatic image building
- There are additional build trigger options that provide more customization in the CI/CD pipeline

## Understanding docker registry
- can be installed locally on your own network to host private images, comes with most feautures (encryption, authentication, etc)
- backend stoarge drivers can be integrated with amazon S3, google, microsoft, other storage solutions
- you can enable a registry-mirror system to copy used base images into a your private storage for failsafe and download speeds

## Run a Private Docker Registry
- runs on port 5000
- you must re-tag images to new registry
- Docker is "Secure by Default" and will only talk over HTTPS (TLS)
```bash
docker container run -d -p 5000:5000 --name registry registry
docker run some-container
docker tag some-container 127.0.0.1:5000/some-container
```
- to host registry in volume: `docker container run -d -p 5000:5000 --name registry -v $(pwd)/registry-data:/var/lib/registry registry

## Assignment: Secure Docker Registry with TLS and Authentication
- Built my registry using play-with docker, enabled TLS, added users

## Private Docker Registry with Swarm
- very similar to before, uses `docker service` commands instead
- even in swarm, all nodes can use the same 127.0.0.1:5000 due to docker routing mesh
- You must decide how to store images (volume driver)
- You have to set up to allow all nodes to access the image repository
- If you dont want to use docker registries there are many open-source options as well as ASW, Azure and google cloud.
