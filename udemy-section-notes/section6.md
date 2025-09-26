# Section 6

## Container Lifetime & Persistaint Data
- contaienrs are usually immutable and ephemeral
- immutable infrastructure: only re-deploy container, never change
- "seperation of concerns": application can be rebuilt in a container but the unique data can be kept persistant in a volume (persistant data)
- There are volumes and Bind Mounts
    - volumes: make special location outside of contianer UFS
    - Bind Mounts: link contrainer path to host path

## Persistant Data: Data Volumes
- you can define a volume in a dockerfile: `VOLUME /path/to/volume`
- Volumes upon creation are tagged with a 64 character random ID
    - You can name the volumes to be more clear by using a `name:path/to/volume`
    - EX: `docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql`
- you can create a volume seperately than during runtime in a container or in the dockerfile: `docker volume create `

## Persistent Data: Bind Mounting
- Maps a host file or directory to a container file or directory
- host files overwrite any file in container, skipping UFS(Union file system)
- bind mounts can only be put in the command line when running the container, not in the dockerfile.
- EX format: `docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx`

## Postgres Container
- remember that postgres requires a password when starting the container unless you specify that it doesnt need it: `POSTGRES_HOST_AUTH_METHOD=trust` or set the password `POSTGRES_PASSWORD=mypasswd`

## Assignment 1 (assignment 1 for new notes): Named Volumes
- The goal of this assignment is to learn how to read up on images in docker hub to find where containers expect volumes to be placed. Using a volume as a database makes so that updating to a new version such as postgres allows you to keep the same schemas while keeping your software up to date.
- Here is the code I ran:
    ```bash
    docker volume create psql
    docker run -d --name psql1 -e POSTGRES_PASSWORD=mypassword -v psql:/var/lib/postgresql/data postgres:15.1
    docker logs psql1
    docker stop psql1
    docker run -d --name psql2 -e POSTGRES_PASSWORD=mypassword -v psql:/var/lib/postgresql/data postgres:15.2
    docker logs psql2
    docker stop psql2
    ```

## File Permissions Across Multiple Containers
- The Linux Kernel only cares about IDs, which are attached to each file and directory in the file system itself. However when a container access mounted or volume files it can label the folders or files differently. But the underlying ID and file should be the same. 
- to trouble shoot permissions in docker containers you can run `ps aux`. each process listed needs a matching user ID or group ID to access files.
- Sometimes the best solution is to create a new user in one Dockerfile and setting the startup user with USER. (USER docs)[https://docs.docker.com/engine/reference/builder/#user]
- When setting a Dockerfile's USER, use numbers, which work better in Kubernetes than using names.
- if `ps` doesn't exist in you container, you can always install it: `apt-get update && apt-get install procps`

## Assignemnt 2: Edit code running in containers with bind mounts
- The main premise will be to use bind mounts to add developing capabilities to you web server without actually changing anything on the host.
- Succesfully ran the server using a volume and changed the file dynamically, seeing the containerized website change in real time
