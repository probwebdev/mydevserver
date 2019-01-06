# Docker Swarm
**Operator**: Traefik, Portainer  
**Services**: Nginx, Postgres, Drone, Gitea, Docker Registry, MTG Proxy

## Initial setup
1. Setup configuration:
	- Copy Compose and Traefik config dist files
	- Create **traefik/acme.json**, and do `chmod 600`
	- Create **secrets/do_auth_token.txt**, put DigitalOcean token inside (for Let's Encrypt DNS Challenge) and do `chmod 600`
	- Inside **secrets/htpasswd.<service_access_name>** generate _user:password_ pair - `htpasswd -cB htpasswd user` to use Basic Auth, do `chmod 644`
	- Create registry secret with `openssl rand -hex 16` and put inside services.env
	- Configure **docker-compose.yml**
2. Create required external docker volumes
3. Create external (optionally attachable) networks: _shared_ and _services_, e.g. ` docker network create --driver=overlay --attachable shared`
4. Initialize a swarm `docker swarm init --advertise-addr=<ip.addr>`
5. Add constraint labels to node/nodes `docker node update --label-add <service_name>=true <node>`
6. Deploy a swarm stack `docker stack deploy -c docker-compose.yml operator`
7. Within Portainer create required services stacks using preconfigured **docker-compose.\*.yml** and services.env

## NOTES:   
After first db initialization you will have to create separate users and DBs for Drone and Gitea from within container, e.g:
- `createuser drone -d -c 16 -E -P -S -U postgres`
- `createdb drone -O drone -U postgres`
