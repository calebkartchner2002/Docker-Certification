# Section 10: Swarm Basic Features and How to Use Them In Your Workflow

## Scaling Out with Overlay Networking
- when you `docker create network` you can add `--driver overlay` to allow container-to-container traffic inside a single swarm.
- you can optionaly create IPSec (AES) encryption
- Each service can be connected to multiple networks. EX: frontend and backend networks
- creating custom swarm network for a postgres database: `docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=mypass postgres`
- even after hosting one drupal service on one of three nodes, you can access it from the IP of any node. Docker swarm internally can reroute traffice to the place it needs to go.

## Scaling Out with Routing Mesh
- Docker swarm has a routing mesh
    - this routes incoming packets for a service to proper task
    - There is automatic use of IPVS and load balancing across services
    - docker uses a pirvate network for containers to communicate on. Uses VIP
- external traffic can go into any node
    - This allows dynamic routing of packets to the correct location. Good for when a container unexpectly goes down and you don;t want to rewire connections.
- All incoming traffic to any node must go through the load balancer first and then gets rerouted
- Docker swarm is stateless load balancing meaning that a client talking to the server will not get a consitent node talking back

## Assignment 7: Create a Multi-service Multi-Node Web App
- created the Docker Distributed Voting App
- 1 volume, 2 networks, 5 services
- created seperated networks for backend and frontend
- mounted volume on backend network

## Swarm Stacks and Production Grade Compose
- stacks accept compose files as their declaritive definition for services, networks, volumes
    - use `docker stack deploy` 
- no build command, you must use deploy. Build should not be done in production
- stacks will add their name to all objects they create to easily identify them. 
- `docker statck deploy -c compose-file.yml`
    - running this command even when your application is already running will update the stack with new configuration changes in the compose file.

## Secrets Storage for Swarm: Protecting Your Enviorment Variables
- Swarm Raft DB is encrypted on disk on manager nodes, only manager nodes have the keys to decrypt it.
- secrets can be communicated to worker nodes through the control plane with TLS + Mutual Auth
- secrets look like files. They are in `/run/secrets/<secret_name>` with the secret value in the that file location.
- Secrets can only be used in Swarm, there are workarounds in docker compose with mounting files.

## Using Secrets in Swarm Services
- `docker secret create psql_user psql_user.txt`
- you can also `echo` it in: `echo "mypass" | docker secret create psql_pass -`
- normal docker commands come with secrests: ls, inspect etc
- you can use a `--secret` tag in your `docker service create` command for containers to have certain containers have access to variables
- You can add secrest to existing containers but doing this will restart the container and create a new one with the updated secret

## Using Secrets with Swarm Stacks
- secrets can be defined in the compose file (version 3.1 or later)
- secrest file name is defined under service in compose file. Then values are defined in root section of compose file in a `secrets:` section
- permissions and other more technical configurations can be be defined in longer secrests definition
- `docker secret ls` will show all secrets on stack, secrests are also prefixed with stack name.

## Assignment: Create stack w/ Secrets
- This is use of secrests with the former assignment 5

