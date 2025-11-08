# GitHub Actions Workflow Examples

## Assignments for this Section
- What is GitHub Actions: https://www.youtube.com/watch?v=cP0I9w2coGU
- Learning GitHub Actions: https://docs.github.com/en/actions
- List of example lists that I will be modifying: https://github.com/BretFisher/docker-ci-automation
- Easy Assignment: https://github.com/BretFisher/httpenv
- Advanced Assignment: https://github.com/BretFisher/example-voting-app

## Add Basic Docker Build
- Github actions is built in YAML format
- Each Github YAML file is one action `workflow`
    - This can contain many `jobs` 
    - Each `job` can have many `steps`
- Each `workflow` is kicked off by an github `event`. Here is an example with events `Pull request` and `Push`:
```yaml
on: 
    push:
        branches:
            - main
    pull_request:
```
- There are 6 offical docker github actions
    - Logging in: `docker/login-action@v1`
    - Building image: `docker/build-push-action@v2`
        - specific tags can be specified
        - you can put conditional states for example only allow this to run events that arent a `pull_request` which you would not want to do for a building image command.

## Add Buildkit Cache
- Buildkit will cache layers of images when building images for github actions
- Buildkit still follows OCS rules for building and formatting containers but provides more features and quicker deployment and testing of containers
- Buildkit will utilize the caching not just for the testing but also the deployment across different enviorments.

## Add Multi-Platform Builds
- Enabling QEMU and describing what platforms to use, it will build out images on each of these platforms, EX: intel, Arm based (Apple M1) in parallel
- You must add QEMU as a step underneath a job
- You then must add in your docker build step the platforms in which you want to build on

## Add Metadata and Tags
- Metadata is a specific action in docker and it allows intellince for labels and tags in images
- Helpful when you want different labels and tags when doing pull requests or pushing to prod, etc
- with a few lines of yaml in the workflow file this can be enabled

## Add GitHub Comments
- You can have automatic comments be made telling you the docker image tag so you know
- you can add a rule in the yaml that creates a rule to upon pull request to find docker image tag and write it out in comment

## Add CVE Scanning of Images
- You almost always will have vulnerability in images and its hard to get it down to zero but the more that can be fixed the better. Many are simple fixes.
- It is best to start off easy and have the CVE give results without interupting workflow, use `exit-code: 0`. Then once vulnerbailties are all addressed you can implement scan blocking to ensure your containers remain safe in the future

## Add CVE Scan Blocking
- you can add `permissions` which you can set read and write access to your git code. 
- you can allow docker to first look for any vulnerabilities and write that out to logs
- You then can specify a second round of scanning just for `severity: HIGH CRITCAL` which if returned true will use `exit-code: 1` and block image from being built. 

## Add Unit and Integration Testing
- You will build an image for testing and then `docker run --rm` in the container. If this doesn't exit with 1, it will then build a production image with correct tags and settings.
- Integration testing usually needs other things to be available, in docker compose you can add `condition` and `service_healthy` for containers that need to be up and running before the integration test can be run  
- you can specify exactly which exit code to look at out of all the running containers using the `docker compose --exit-code-from <container>`. This should be the main container that intereacts with the other but is where the heart of the integration test lies. 

## Add Kubernetes Smoke Test
- you use the github containe registry as the "testing registy", use docker hub as the production registry
- you can use `uses: k3d-action@v2`, this is an action that builds a cluster using `K3s` insdie of `k3d` which is a docker container
- These can ussualy be pretty quick, around 30 seconds for a medium size system.

## Add Job Paralleization to GHA
- you can use `needs: [build-test-image]`. This allows you to first build the image and then for all of your different tests that use that image, you can run the tests in parallel in seperate containers. 
