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

### Faster initial sync (multi-core machines)

The first run has to sync the Bitcoin block chain. btcd's default cache is
small, and inside a container its CPU auto-detection often under-counts the
cores it can use. On a multi-core host you can speed up the initial sync with
the `multi-core-docker-compose.yml` override, which raises `-dbcache` and pins
btcd's script-verification threads (`-par`):

```
docker compose -f docker-compose.yml -f multi-core-docker-compose.yml up -d
```

It only overrides btcd's `command:`; everything else is inherited from the
base file. Tune `-par` / `-dbcache` to your machine in that file.

### Incident Response

A short runbook for operators. When in doubt, capture logs first
(`docker logs --since 1h go-vsc-node > incident.log 2>&1` — the `2>&1` captures
stderr too, which a bare `>` would drop) and reach the core team on
[discord](http://discord.gg/yvGXZsQTU6) before taking destructive action.

- **Suspected key compromise** (the active key in
  `./data/config/identityConfig.json` leaked). Stop the node
  (`docker compose down`), rotate the account's **active key** on Hive L1
  (this requires the account's owner key), update `identityConfig.json` with
  the new key, then
  `docker compose up -d`. Notify the team so other witnesses are aware. Treat
  the old key as burned.

- **Chain halt / node stuck** (no new blocks in the logs). Check
  `docker logs go-vsc-node -f` for panics and peer count. A restart
  (`docker compose restart go-vsc-node`) clears most transient stalls. If the
  Mongo state looks corrupt, do **not** wipe `./data/vsc-db` blindly — ask the
  team; a full resync is slow and a bad restore can leave you on a diverged
  state.

- **btcd not syncing.** Inspect `docker logs vsc-btcd -f` and
  `docker exec vsc-btcd bitcoin-cli -rpcuser=vsc-node-user -rpcpassword=vsc-node-pass getblockchaininfo`
  (watch `blocks` vs `headers` and `verificationprogress`). No peers → check
  outbound connectivity; slow IBD → use the multi-core override above. Because
  go-vsc-node gates on `btcd: service_healthy`, a wedged btcd shows up as
  go-vsc-node never starting.

- **Stuck withdrawal / action.** These are L2 protocol state, not something to
  fix by editing Mongo. Record the affected transaction id and account, grab
  the relevant log window, and report to the team — manual DB surgery on
  consensus state can desync your node from the network.

- **Bad auto-update (rollback).** Watchtower tracks the `:main` tag, so a bad
  push can roll forward automatically. To pin and roll back: set
  `AUTO_UPDATE=false` (stops Watchtower touching the containers), find the
  last-good image with `docker images --digests vscnetwork/go-vsc-node`, pin
  that digest in `docker-compose.yml` (e.g. `image:
  vscnetwork/go-vsc-node@sha256:...`), then
  `docker compose up -d --force-recreate go-vsc-node`. Re-enable auto-update
  once a fixed image is published.

- **Security contact / disclosure.** Report vulnerabilities **privately** to
  the core team via discord — do not post exploitable details in public
  channels. We follow responsible disclosure and will coordinate a fix and
  rollout window with operators.

### Maintenance

The node is designed to self-update as necessary. However, on rare occasions, the deployment configuration may require manual updates not covered by automatic updates. Should such a situation arise, we will inform the community through our usual communication channels [discord](http://discord.gg/yvGXZsQTU6) and [twitter](https://twitter.com/vsc_eco).

You can disable automatic updates by setting the environment variable `AUTO_UPDATE` to _false_. However, we recommend to keep this feature enabled to ensure the node is always up-to-date. In our rapidly evolving ecosystem, it's crucial to keep the node updated for optimal network health.
