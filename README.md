# VSC Node deployment

This repository hosts the Docker Compose file necessary for deploying the VSC node.

### Prerequisites

Install [Docker](https://docs.docker.com/get-docker/) and [Docker compose](https://docs.docker.com/compose/install/).

Clone this repository to a desired location. It's crucial to ensure the Docker user has write permissions in the directory where you plan to initiate the Docker Compose file.

Next, create an `.env` file at the root of this repository. Populate this file with your specific configurations, guided by the schema provided in the [.env.example file](https://raw.githubusercontent.com/vsc-eco/vsc-deployment/main/.env.example). Alternatively, use the configuration template provided below:

``` txt
# Fill these in with your hive account details
HIVE_ACCOUNT=
HIVE_ACCOUNT_POSTING=
HIVE_ACCOUNT_ACTIVE=


# Leave untouched unless instructed otherwise.
MULTISIG_ACCOUNT=vsc.beta
MULTISIG_ANTI_HACK=did:key:z6Mkj5mKz5EBnqqsyV2qBohYFuvTXpCuxzNMGSpa1FJRstze
MULTISIG_ANTI_HACK_KEY=
```

### Starting Up

To launch the node, execute `docker compose up -d` from the command line (or `docker-compose up -d` depending on your docker compose version).

For real-time log observation, use `docker logs vsc-node -f`.

### Maintenance

The node is designed to self-update as necessary. However, on rare occasions, the deployment configuration may require manual updates not covered by automatic updates. Should such a situation arise, we will inform the community through our usual communication channels [discord](https://discord.gg/tm7YkW7A) and [twitter](https://twitter.com/vsc_eco).

You can disable automatic updates by setting the environment variable `AUTO_UPDATE` to _false_. However, we recommend to keep this feature enabled to ensure the node is always up-to-date. In our rapidly evolving ecosystem, it's crucial to keep the node updated for optimal network health.

