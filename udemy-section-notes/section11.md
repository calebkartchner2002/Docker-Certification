# Section 11: Swarm App Lifecycle

## Using Secrets With Local Docker Compose
- compose bind mounts secrets to the container during runtime, this is not safe but okay for dev and test.
- This only works with file based secrets.
- Allows you to test in dev like you actually run in production

## Dev, Build, and Deploy With a Single Compose Design
- if you have multiple compose files for different test, dev and prod instances, you can put `docker-compose.override.yml` and this will always be the default compose unless otherwise selected.
- Having multiple compiles allows you to specify one specifically for testing which can be used for automatic test deployments with suplemental sample data volumes. This is much faster and simpler than using production compose and trying to edit it to adjust to test every deployment.
- you can use `docker compose -f docker-compose.yml -f docker-compose.prod.yml config > output.yml` to run multiple compose files as a single compose file. 
- You can use the docker extends command to dynamically add other `yml` file configurations into your own. This way no files have to have repeated sections in them, they can just be a conglomerate of needed configurations. EX: `common-services.yml`

## Changing Things in Flight
- swarm will update tasks/containers on a rolling basis, limits downtime
- There are many cli options to control update capablities, strategy, etc
    - To update with new images in docker service: `docker service update --image <image> <servicename>`
    - You can add env and change ports without downtime: `--env-add SOMETHING=something --publish-rm 8080`
    - There is also a docker service scale to increase the number of replicas of an instance: `docker service scale web=8 api=6`
     
## Healthchecks in Dockerfiles
- Supported in all docker tools: swarm, stack, service, compose
- There are three container states: `starting`, `healthy`, `unhealthy`
- healthchecks is very basic in principle. It will not replace external monitoring systems
- healthcheks are run with a docker exec `curl` command in the container. This must return either a `1` or `0` otherwise docker will not respond.
-  containers can sometimes be unstable and fail and restart. Because of this dockerhealth cheks must recieve by default 3 unhealhy returns with a default 30 second interval. If all three fail then it assumes something more fundumental has failed.
- Many containers or services come with default healthcheck functionality. EX: postgres has a `pg_isready` CMD statement
