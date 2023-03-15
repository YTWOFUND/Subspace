# Subspace

### Subspace node installation instructions

## [Official documentation](https://docs.subspace.network/docs/protocol/substrate-cli#i-pre-requisites)

System requirements: </br>
CPU: 4 Core </br>
RAM: 8 Gb </br>
SSD: 50 Gb </br>
OS: Ubuntu 20.04 LTS </br> 

Network configurations: </br>
Open the following ports: 30333, 30433, 30533 </br>

# Installation of the Subspace node with Docker.

1. Install wallet [SubWallet](https://subwallet.app/)

2. Let's create a folder subspace and go to the folder.
```
mkdir subspace && cd subspace
```
3. Create a docker-compose.yml file
```
nano docker-compose.yml
```
4. Copy completely and paste into nano
```
  version: "3.7"
  services:
    node:
      # For running on Aarch64 add `-aarch64` after `DATE`
      image: ghcr.io/subspace/node:gemini-3c-2023-mar-14
      volumes:
  # Instead of specifying volume (which will store data in `/var/lib/docker`), you can
  # alternatively specify path to the directory where files will be stored, just make
  # sure everyone is allowed to write there
        - node-data:/var/subspace:rw
  #      - /path/to/subspace-node:/var/subspace:rw
      ports:
  # If port 30333 or 30433 is already occupied by another Substrate-based node, replace all
  # occurrences of `30333` or `30433` in this file with another value
        - "0.0.0.0:30333:30333"
        - "0.0.0.0:30433:30433"
      restart: unless-stopped
      command: [
        "--chain", "gemini-3c",
        "--base-path", "/var/subspace",
        "--execution", "wasm",
        "--blocks-pruning", "archive",
        "--state-pruning", "archive",
        "--port", "30333",
        "--dsn-listen-on", "/ip4/0.0.0.0/tcp/30433",
        "--rpc-cors", "all",
        "--rpc-methods", "safe",
        "--unsafe-ws-external",
        "--dsn-disable-private-ips",
        "--no-private-ipv4",
        "--validator",
  # Replace `INSERT_YOUR_ID` with your node ID (will be shown in telemetry)
        "--name", "INSERT_YOUR_ID"
      ]
      healthcheck:
        timeout: 5s
  # If node setup takes longer than expected, you want to increase `interval` and `retries` number.
        interval: 30s
        retries: 5

    farmer:
      depends_on:
        node:
          condition: service_healthy
      # For running on Aarch64 add `-aarch64` after `DATE`
      image: ghcr.io/subspace/farmer:gemini-3c-2023-mar-14
      volumes:
  # Instead of specifying volume (which will store data in `/var/lib/docker`), you can
  # alternatively specify path to the directory where files will be stored, just make
  # sure everyone is allowed to write there
        - farmer-data:/var/subspace:rw
  #      - /path/to/subspace-farmer:/var/subspace:rw
      ports:
  # If port 30533 is already occupied by something else, replace all
  # occurrences of `30533` in this file with another value
        - "0.0.0.0:30533:30533"
      restart: unless-stopped
      command: [
        "--base-path", "/var/subspace",
        "farm",
        "--disable-private-ips",
        "--node-rpc-url", "ws://node:9944",
        "--listen-on", "/ip4/0.0.0.0/tcp/30533",
  # Replace `WALLET_ADDRESS` with your Polkadot.js wallet address
        "--reward-address", "WALLET_ADDRESS",
  # Replace `PLOT_SIZE` with plot size in gigabytes or terabytes, for instance 100G or 2T (but leave at least 60G of disk space for node and some for OS)
        "--plot-size", "PLOT_SIZE"
      ]
  volumes:
    node-data:
    farmer-data:
```
Edit the file with the necessary values: </br>
INSERT_YOUR_ID - desired name to be displayed in telemetry. </br>
WALLET_ADDRESS - the address of your wallet created earlier. </br>
PLOT_SIZE - per plot size in gigabytes or terabytes, such as 100G or 2T (but leave at least 10G of disk space for the node). </br>


5. Launching Subspace installation
```
cd subspace && docker-compose up -d
```

# Useful
[Telemetry server](https://telemetry.subspace.network/#/0x9ee86eefc3cc61c71a7751bba7f25e442da2512f408e6286153b3ccc055dccf0) </br>
[Block Explorer](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Feu.gemini-1b.subspace.network%2Fws#/explorer) </br>

Logs
```
cd subspace && docker-compose logs --tail=1000 -f
```

Delete
```
cd subspace && docker-compose down -v
```
