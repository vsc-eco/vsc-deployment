# VSC Node deployment

This repository hosts the Docker Compose file necessary for deploying the VSC node.

### Setup

1. Install [Docker](https://docs.docker.com/get-docker/) and [Docker compose v2](https://docs.docker.com/compose/install/).

2. `git clone https://github.com/vsc-eco/vsc-deployment`
   Clone this repository as a normal user (not root/admin) to a desired location. It's crucial to ensure the Docker user has write permissions in the directory where you plan to initiate the Docker Compose file.

3. `docker compose run init`
   Initialize the configuration files

4. Edit the config file located at `./data/config/identityConfig.json` and be sure to add in your Hive username and active key

5. `docker compose up -d`
   Start the Docker containers. This will add a GraphQL server on port 8080, a MongoDB instance on port 27021, and a libp2p connection on port 10720.

### Starting Up

To launch the node, execute `docker compose up -d` from the command line (or `docker-compose up -d` depending on your docker compose version).

For real-time log observation, use `docker logs go-vsc-node -f`.

### Maintenance

The node is designed to self-update as necessary. However, on rare occasions, the deployment configuration may require manual updates not covered by automatic updates. Should such a situation arise, we will inform the community through our usual communication channels [discord](http://discord.gg/yvGXZsQTU6) and [twitter](https://twitter.com/vsc_eco).

You can disable automatic updates by setting the environment variable `AUTO_UPDATE` to _false_. However, we recommend to keep this feature enabled to ensure the node is always up-to-date. In our rapidly evolving ecosystem, it's crucial to keep the node updated for optimal network health.

#### Image moved to GitHub Container Registry

The node image is now published as `ghcr.io/vsc-eco/go-vsc-node:main`, built by GitHub Actions in [vsc-eco/go-vsc-node](https://github.com/vsc-eco/go-vsc-node). The old `vscnetwork/go-vsc-node` image on Docker Hub is no longer updated, and automatic updates cannot switch you over by themselves. If your node was set up before this change, run once:

```sh
git pull
docker compose up -d
```

Then check that `docker ps` shows the `ghcr.io/vsc-eco/go-vsc-node:main` image.

Testnet nodes run the `develop` image instead: in `docker-compose.yml`, comment out the `:main` image line and uncomment the `:develop` one (in both the `init` and `go-vsc-node` services).
