# Section 25: Docker 19.03 Release New Features

## BuildKit and the new Docker buildx CLI
- buildx is an extended docker build using BuildKit as its backend
- enables multi architecture builds (amd64, arm64, etc)
- allows parallel builds across multiple nodes
- automatically creates and pushes manifest lists (maps a tag to multiple architecture specific images)
- creating and using a builder:
```bash
docker buildx create --name dckbuilder --driver docker-container
docker buildx use dckbuilder
docker buildx ls
```
Multi-platform build and push:
```bash
docker buildx build \
    --platform linux/amd64,linux/arm64,linux/arm/v7 \
    -t username/image --push .
```
- buildKit runs inside a build container when using a docker container driver.

## Docker Context and SSH Connections
- docker context lets you switch between multiple Docker environments (local, remote, SSH, etc)
- replaces manually setting `DOCKER_HOST` environment variables
- contexts store connection info in `~/.docker/contexts/` as json files
- supports docker endpoints and kubernetes endpoints in a single context
- Listing and using contexts:
```bash
docker context ls
docker context use <name>
```
- creating a context for a remote docker daemon through TCP:
```bash
docker context create --docker "host=tcp://IP:2376" my-remote
```

## Docker App and Image Packaging of Compose YAML
- docker app is a tool to package, share and deploy multi-service apps built with Docker Compose
- docker app uses container images and registries to distribute apps
- new versions are rebuilt on CNAB (cloud native application bundle) under the hood
- can deploy to docker swarm and kubernetes using the same app definition
- downloading docker app gives two binaries:
    - standalone CLI (`docker-app`) which can run on its own
    - CLI plugin binary (placed under `~/.docker/cli-plugins/docker-app`) which integrates as docker app subcommand
- To use CLI plugins, enable experimental mode for the Docker client:
    - this is locationed in `~/.docker/config.json` as variable `experimental`

- apps can define parameters for values inside the compose/stack file:
    - ports
    - replicas
    - CPU/memory limits
    - other configuration values
- docker app lets you inspect and override parameters at install time such as changing a default port to `8088`

## Rootless Mode in Docker Engine
- the goal is to run the Docker daemon and containers as an unprivileged user and not `root`
- even if a container escape happens, attacker only gets the regular user and not full root on the host
- devs without `sudo`/root on a shared box can still run Docker
- operators can give each user their own isolated Docker daemon in `$HOME` instead of one root daemon for everyone

- rootless mode creates a systemd user service under your home (`~/.config/systemd/user/docker.service`)
- rootless runs a `dockerd-rootless` binary inside your home directory (`~/bin/...`)
- stores Docker state under user-scoped paths: `~/.local/share/docker/...` (images, containers, overlays)
- the docker socket is not the usual `/var/run/docker.sock`, but a user socket path in your home / runtime directory
- to use rootless Docker with the CLI: `export DOCKER_HOST=unix://<path-to-rootless-docker.sock>`

- rootless docker cannot bind privileged ports (<1024) because the daemon is not root:
    - `-p 80:80` fails as this is a privileged port
    - `-p 32768:80` is a high port so it works
- Other network operations that require low level kernel changes (extra routes, certain virtual interfaces, some overlay features) are limited or unavailable
- swarm can run in rootless but has very limited capabilities so is almost never worth it
- rootless containers can only mount files or directories that your user can access

## Docker Desktop Enterprise
- options:
    - DDC is the free community version.
    - DDE is the paid enterprise version. Includes version packs, locked settings, and application designer
- admins can preconfigure & lock CPU, memory, proxy, and daemon settings
- ensures consistent setups across all developer machines.
- Application Designer
    - GUI to create apps from enterprise-defined templates
    - templates include Dockerfiles, compose files, code, env vars, configs
    - stored in registry (DTR for example) and updated centrally
    - automatically scaffolds and starts multi-container apps using compose

## Docker Desktop Enterprise Clusters
- is a CLI plugin (`docker cluster`) for creating and managing full server clusters
- successor of docker certified infrastructure
- provides lifecycle management for clusters. EX: `create, upgrade, scale, backup, delete`
- one YAML file defines your entire cluster
- is a feature commonly supported among big cloud providers
