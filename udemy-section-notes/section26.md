## Section 26: Devops and Docker Clips
- NOTE: This is a very long section with a few very unrelated topics that I am choosing to pass over. Will try and focus on useful ones in practice and for my project and docker certification exam.


## Are Alpine Base Images Secure
- container and image scanners detect known vulnerabilities in OS packages and dependencies
- scanners rely heavily on OS vendors providing metadata.
    - with vendors like debian, ubuntu and redHat, support is strong. 
    - `alpine` support is weak
- alpine is very small (~5MB) so there is fewer packages and theoretically fewer vulnerabilities
- size usually isnt a major concern in cloud environments — disk is cheap compared to CPU, memory and network costs
- alpine uses different tooling and filesystem locations which could break apps or require changes (EX: issues with `Node.js` tools like nodemon)
- scanning alpine images can miss vulnerabilities because the mapping of packages to CVEs is incomplete
- official docker images default to debian for reliability and broad compatibility
- ubuntu and debian images continue shrinking over time by removing non-essential utilities
- the best practice is to stick with what you know unless you specifically need Alpine for extremely small environments or edge devices

## Apache Web Server Design: many sites in one container
- Apache servers have the ability to run many websites in one daemon
- generally when using an orchestrator, it is best to have more containers and isolate any possible failure points
- generally each repository you want registered with one container rather than putting 10 different ones together to run 10 different websites
- Seperating allows scaling based on ussage

## Dccker Network IP Subnet Conflicts with Outside Networks
- the docker network driver `bridge` is default. There are many others you can choose from. Swarm has its own.
    - you can also choose `None` to have no network driver
- Using `bridge` exposes the subnets to the outside host meaning you cannont use any of the same subnets on your docker network as is used on the host
- You can manually change used subnets on your docker Desktop or whichever engine is being used

## Raspberry Pi Development in Docker
- Raspberry Pi uses ARM64, not x86 so you must use arm64 base images in `Dockerfiles`
- docker desktop can emulate ARM64 using QEMU
- generally you want to build and test ARM64 images on your windows machine and only run on the Raspberry Pi. This is for faster testing and development times and eaiser debugging.
- a great resource is Alex Ellis blog for docker with Raspberry Pi examples

## Should you move Postgres to Containers
- Databases in containers is more nuanced, best solution is to use cloud hosted solutions but it works for both bare metal and containers
- efficency of database on bare metal is the same as running in a container as both are running on the host OS
    - It is important to make sure volumes are configured correctly to ensure similar input/output times and logging
- For scaling to have complete seperate databases then containers are good
- to share the same database base software, bare metal could be better
- Having everything in containers make management easier

## Using Supervisor to Run Multiple Apps in a container
- You can use Supervisor or scripting to run multiple apps in a container. Recommended to only do this in extreme situations where apps are highly dependent and server as "one" core functionality
- SupervisorD can be good for communciation efficency where you want two things that need faster communication to live on the same container (EX: PHP sockets)

## Docker compose or swarm for one server
- production should ussualy run swarm
    - rolling updates with swarm

## Docker Enviorment Configs, Variables, and Entrypoints
- avoid hard-coding environment-specific values (EX: db host, API keys, feature flags, memory settings)
    - set production defaults in Dockerfiles, then override per-environment using
    - `docker run -e`, `Compose environment:` blocks, `.env` files, or orchestrator configs
- legacy apps may require environment variables to be written into config files at startup using an entrypoint script to translate env vars into files
- reference official images (EX: MySQL) for patterns: entrypoint scripts parse env vars + secrets files
- secrets should not live in env variables. there is risk of leaking to logs, debug output, crash traces
- use orchestrator secrets (Swarm or kubernetes) which mount as files
- compose templating and override files reduce repetition and simplify multi service config

## Multiple Docker images from one git repo
- You can have many names for dockerfiles or compose files, default is `Dockerfile` and `compose.yml`. When building or composing, just specify input file
- Set build path during `docker build` to be the subdirectory where the `Dockerfile` is built from. Otherwise you could could overlap contexts

## How to use external storage in Docker
- containers do not change the underlying capabilities or limitations of storage systems
    - for example AWS EBS volumes can attach to only one node at a time and cannot support multi-container read/write workloads

## Startup Order With Multi-Container Apps
- every container must have a set option to do upon failure: restarting, stopping
- docker compose has healthchecks which can difine dependencies upon container startup
