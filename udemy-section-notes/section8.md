# Section 8

## Docker Compose and The docker-compose.yml File
- `docker-compose.yml` is used to configure relationships between containers
- format:
    - version
    - services (containers)
    - networks
    - volumes
- options defined in docker run comands will be defined in the YAML. EX: `ports:` `- 80:4000` and `volumes:` `- ./:site`
- servicenames are defined under containers to name each container's DNS. You will give these names related to the container
    - enviorment variables can be defined as well as enviorment files.
- CLI tool `docker compose` is used to interact with entire suite of container defined in `docker-compose.yml`

## Trying Out Basic Compose Commands
NOTE: docker updated `docker-compose` in 2022 with the new format: `docker compose`. Both commands still work.
- simplies onboarding to get invovled in developing an suite of applications. Compose allows you to clone the repostiory and just run `docker compose up` which should bring up the whole system.
- `docker compose up`: sets up volumes, networks and start all containers
- `docker compose down`: stop all containers and remove containers, volumes, networks

## Version Dependencies in Multi-Tier Apps
- every app with dependencies, will also have version requirements for those dependencies.
- If you add an app and a database to a Compose file, then that app is going to have specific database versions it is compatible with
- Docker and Compose are used to control versions of apps.

## Assignmment 5: Writing a Compose File
- lookup compatible versions with used apps
- if you name your volumes such as `drupal-modules:/var/www/html/modules` under a service then you must define those volumes at the bottom of your file as well.
- docker inspect will show exposed ports in which the default image is able to connect on.

## Adding Image Building to Compose Files
- There are specific enviorment variables that can only be defined during build: docs.documentation.com/dockerfiles
- You can define a build section under a container service in your docker compose file
    - you can give `context`, this will provide location to automatically build docker container
    - you can use `dockerfile` to specify which docker file if there is multiple
    - you can specify the `image` name after build as well. Names will be default named if not specified: `current_directory_servicename`
    - you can use `docker compose down --rmi local` to remove images after composing down only if default image names were assigned. if you use the `--rmi all`, this will remove all images defined in the compose file.

## Assignment 6: Startup Scripts
- defining you own custom build during compose
