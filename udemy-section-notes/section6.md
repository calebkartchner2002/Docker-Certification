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
