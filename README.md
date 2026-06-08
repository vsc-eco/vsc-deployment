# VSC Node deployment

This repository hosts the Docker Compose file necessary for deploying the VSC node.

### Setup

1. Install [Docker](https://docs.docker.com/get-docker/) and [Docker compose v2](https://docs.docker.com/compose/install/).

2. `git clone https://github.com/vsc-eco/vsc-deployment`
   Clone this repository as a normal user (not root/admin) to a desired location. It's crucial to ensure the Docker user has write permissions in the directory where you plan to initiate the Docker Compose file.

3. `cp .env.example .env` and set a strong, unique `MONGO_PASSWORD`.
   MongoDB now requires authentication (VD-H1): the database stores all chain
   state, and an unauthenticated instance is readable/writable by any other
   process or container on the host. Compose refuses to start until
   `MONGO_PASSWORD` is set.

   > **Upgrading an existing node** whose `./data/vsc-db` was created before
   > authentication was enabled: the `MONGO_INITDB_ROOT_*` variables only create
   > the user on a *fresh* data directory, so create it once manually —
   > `docker compose up -d mongo`, then
   > `docker exec -it mongo_vsc mongosh admin --eval 'db.createUser({user:process.env.MONGO_USER,pwd:process.env.MONGO_PASSWORD,roles:[{role:"root",db:"admin"}]})'`
   > — and restart the stack. Fresh installs need no manual step.

4. `docker compose run init`
   Initialize the configuration files

5. Edit the config file located at `./data/config/identityConfig.json` and be sure to add in your Hive username and active key

6. `docker compose up -d`
   Start the Docker containers. This will add a GraphQL server on port 8080, a MongoDB instance on port 27021, and a libp2p connection on port 10720.

### Starting Up

To launch the node, execute `docker compose up -d` from the command line (or `docker-compose up -d` depending on your docker compose version).

For real-time log observation, use `docker logs go-vsc-node -f`.

### Maintenance

The node is designed to self-update as necessary. However, on rare occasions, the deployment configuration may require manual updates not covered by automatic updates. Should such a situation arise, we will inform the community through our usual communication channels [discord](http://discord.gg/yvGXZsQTU6) and [twitter](https://twitter.com/vsc_eco).

You can disable automatic updates by setting the environment variable `AUTO_UPDATE` to _false_. However, we recommend to keep this feature enabled to ensure the node is always up-to-date. In our rapidly evolving ecosystem, it's crucial to keep the node updated for optimal network health.
