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

## Assignment: Named Volumes
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
- The Linux Kernel only cares about IDs, which are attached to each file and directory in the file system itself, and those IDs are the same no matter which process accesses them.
