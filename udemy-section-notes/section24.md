# Section 24: Docker Security Good Defaults and Tools

## Docker Cgroups and Namespaces
- 2 main things in Linux allow docker to be possible: 
    - Kernel Namespaces: can be used to limit access resources, networking, files on disc, etc. You can't see anything else accept your own bubble. This is how docker is run on a system. 
    - Control Groups: probits what usage a container can use. This includes RAM, memory, etc. Usually this is not set by default and containers can use all resources. In production it is good practice to put a high threshold limit so as to not let one container take up all of the space from the other containers
- There are a ton of security tools that can be used to make sure these aspects are done correctly. However some or many of these require entire servers to run auditing and cost a significant amount of money. This tends to be for more high end financial and government systems.

## Docker Engine's Out-Of-The-Box Security Features
- There have been several instances of malicous images being run on peoples systems that mine crypto currnency or do other malicious activity.
- By default docker "hardens" the system so that the containers cannot have access to anything outside the container
- `seccomp`, `AppArmor` `SELinux`, `Kernel Capability` are all security features by docker that are enabled by default
- Most developers of containers (largely done by docker team), has gone through applications such as `nginx` and ensured that anything that the application doesn't need access to, gets taken away. Big difference between running on host or in a container security wise.

## Docker Bench, The Host Configuration Scanner
- Docker Bench is a free tool made by docker that can be run on the host that has docker installed and will check your configurations
- Did docker get installed correctly? does it have the right capablities? It checks for these security holes
- This is a command line tool that should be used on systems that are running your docker systems
- Likely there will be many security warnings and errors that you can't resolve, awareness and decreasing as many as you can is important

## Using USER in Dockerfiles to Avoid Running as Root
- running applications or software using python or other common programming languages generally defualts the user as root.
- Official docker images such as `Node` run under `root` even through they define a `User` role. 
    - It is the developers responsibility to use the `User` that is defined by offical docker images. Some don't define this but you can add this at the top of the container.
    - You can define the User in a dockerfile using `USER node`
    - When not using `root`, it is best practice to use `chown` to ensure file is restricted to User access: `COPY --chown=node:node . .` 
- there have been instances where a vulnerability in a container has allowed a hacker to use it's `root` access to gain more access on the system.

## Docker User Namespaces for Extra Host Security
- User Namespaces is not setup by default
- can be configured per host in the `docker.json` file
- This tells docker that anytime it runs a container it will be run as a non-root user on the host
- `containerd` and `runc` are run programs that docker uses and by defualt will start the containers under `root`
- This makes so that even if a hacker found a vulnerability in a container and got control of the process they would have the same namespace permissions of a `User` and not `root`
- oftentimes teams will segment two servers to run containers that use `root` vs a `User`. This minimizes the exposure of risk that the system will have.
- Some applications do not have built in functionality to run in a non-root Namespace. Research is usually required to ensure compatability. Most common applications support lower namespace authority.

## Code Repo and Image Scanning for CVE's
- `sneak` is a tool that can be used to scan dependenices in a repo and can give reports on vulnerabilities. Scanning should be done often.
- CVE database is a public list of all vulnerablities, managed by MITRE
- This is what you look for in a scanning tool, one of the best currently is `Trivy`:
    - OS Packages
    - Application Dependencies
    - CI Integration
    - Accuracy (There are many problems with detecting vulnerabilities in `alpine` distros)
- Many Scanners can be added and run in dockerfiles
- Almost all images have vulnerabilities. It is important to know them, acknowledge them and reduce risk to acceptable margins
- If integrated into CI/CD pipeline, make sure to allow `--continue-on-failure` (audit mode), otherwise everything will fail during development

## Sysdig Falco, Content Trust, Custom Seccomp and AppArmor Profiles
- Sysdig Falco essentialy takes a common list of suspect behaviors and logs these actions
    - comes with default list but can be added to.
- Content Trust is set of tools that docker mostly provides to make sure everything in your pipeline gets certified throughout the process to run on your machine
    - Allows you to specifically assign what can be run on your system
- AppArmor, SELinux, Seccomp and Linux capabilities are enabled by default on docker but can be configured to harden images even further. This is good to know for systems that are very important to lock down

## Docker Rootless Mode
- Allows you to run the docker deamon without root but you cannot use virtual networking (this requires root)
- there is a script provided by docker that configures a container to be run by the user: https://get.docker.com/rootless

- Windows is entirely different in terms of security. Most tools don't support windows containers

## Distroless Images
- There is no base package system, no apt or yum
- Most programs need these dependencies and shared libraries liked openssl
- golang, C can build single binaries that have everything they need to run and would be able to run distroless (no layers)
- Very difficult to use and troubleshoot as you cannot exec into the container or use most maintenance techniques
- can be seen as safer as many tools that can be used in an exploit are not available to the hacker, but can be less safe due to most security mechanisms not being able to suppor this mode

## Docker Secrets
- Your application has to have secrets somewhere and will not be encrypted in memory
- if someone has `root` access to a container they will be a able to access secrets
- Secrets are set in a file in RAM which have permissions that you set
- Kubernetes and Swarm encrypt secrests on Disk but are readable in memory and RAM. Already more secure that legacy applications, not a perfect solution.
