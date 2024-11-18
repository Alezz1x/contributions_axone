# Axone Node Setup

This guide will help you set up your validator node for the **Axone** project. It is tailored for the validator **Alezz1x** and is based on official resources and standard practices for node deployment in blockchain ecosystems.

## Prerequisites

Before proceeding with the setup, ensure that your system meets the following requirements:

- A Linux server (Ubuntu 20.04+ recommended)
- 4+ CPU cores
- 8GB+ RAM
- 100GB+ SSD storage
- A reliable internet connection
- A basic understanding of the command line and Linux systems
- Docker and Docker Compose installed on your machine
- Node and NPM installed on your system (for interacting with the Axone ecosystem)

### Step 1: Prepare Your System

#### Update your system packages

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential curl wget vim git
```

#### Install Docker

If Docker is not installed, use the following commands to install it.

```bash
sudo apt install -y docker.io
sudo systemctl enable --now docker
```

#### Install Docker Compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Verify installation:

```bash
docker-compose --version
```

### Step 2: Clone Axone Validator Repository

Clone the Axone validator repository from GitHub:

```bash
git clone https://github.com/axone-xyz/validator.git
cd validator
```

This repository contains the configuration files and scripts for running the Axone validator node.

### Step 3: Configure the Validator Node

Inside the repository directory, locate the configuration files (`config.toml` or similar) where you will set your validator's identity and other configuration parameters.

Edit the configuration file:

```bash
vim config.toml
```

Set the necessary parameters such as:

- **Validator Name**: Choose a unique name for your validator
- **Node API endpoint**: Input the URL for the Axone node you will be connecting to
- **Validator Public Key**: This will be generated when you create your validator key pair

### Step 4: Set Up Validator Keys

Generate your validator key pair. This step is crucial for authenticating and staking as a validator.

Use the Axone command-line tools (if provided by the project) to generate your keys:

```bash
axone-cli keys generate --validator
```

You should save the `validator-private.key` securely. The public key will be used in your validator configuration.

### Step 5: Set Up the Validator Node

Create a `docker-compose.yml` file to define the Axone validator node service. Below is an example of what it might look like:

```yaml
version: '3'

services:
  axone-validator:
    image: axone/validator:latest
    container_name: axone-validator
    restart: always
    ports:
      - "26656:26656"
      - "26657:26657"
    volumes:
      - ./data:/root/.axone
    environment:
      - VALIDATOR_KEY=/root/.axone/validator-private.key
      - AXONE_NODE_API=http://your.node.api.endpoint:26657
    networks:
      - axone-net
    logging:
      driver: "json-file"
      options:
        max-size: "200m"
        max-file: "5"

networks:
  axone-net:
    driver: bridge
```

### Step 6: Start the Validator Node

Once you’ve completed the configuration, you can launch the validator node using Docker Compose.

```bash
docker-compose up -d
```

This will download the necessary Docker image and start your Axone validator node in the background.

### Step 7: Monitor Validator Node

You can monitor your validator node logs to ensure it is running smoothly:

```bash
docker-compose logs -f
```

Alternatively, check the health and status of your validator node via the following command:

```bash
docker exec -it axone-validator axone-cli status
```

### Step 8: Staking Your AXN Tokens

To activate your validator and begin staking, you will need to stake AXN tokens. Use the Axone CLI to perform this action.

First, ensure you have AXN tokens in your wallet:

```bash
axone-cli wallet balance
```

Next, stake your AXN tokens to your validator:

```bash
axone-cli staking delegate --amount 1000AXN --validator YOUR_VALIDATOR_ADDRESS
```

Confirm the transaction and wait for the confirmation on the Axone blockchain.

### Step 9: Stay Updated

It's important to keep your validator node software up-to-date. To update your node, follow these steps:

1. Pull the latest Docker image for Axone:

    ```bash
    docker-compose pull
    ```

2. Restart the container:

    ```bash
    docker-compose down && docker-compose up -d
    ```

### Troubleshooting

If you encounter any issues during setup, here are a few common problems:

- **Node fails to start**: Check if Docker is running and that you have sufficient resources on your machine.
- **Staking issues**: Ensure that the staking transactions are confirmed and that your validator is properly listed on the Axone blockchain explorer.
- **Network connectivity**: Ensure that your server’s firewall allows connections on the necessary ports (typically 26656, 26657).

### Conclusion

Congratulations! You've successfully set up and started your Axone validator node. As a validator, your role is crucial for securing the Axone network and validating transactions. Make sure to keep your node running smoothly and stay up to date with the latest releases from the Axone project.

For more detailed information, you can always refer to the [official Axone documentation](https://docs.axone.xyz/).

---
