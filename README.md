# Bitcoin Development Environment Setup

This repository contains the configuration files necessary to set up a robust, local Bitcoin **Regtest** environment. It accompanies the blog post: **[Setting Up a Local Bitcoin Dev Environment (Regtest, Fulcrum, Mempool, Ord)](https://www.chanalston.com/blog/bitcoin-development-environment-setup)**.

The goal is to create a "mini-mainnet" on your local machine using a hybrid approach (Native Binaries + Docker) for maximum performance and ease of use.

## Contents

| File                  | Purpose                                                                               |
| :-------------------- | :------------------------------------------------------------------------------------ |
| `bitcoin.conf`        | Configures **Bitcoin Core** to run in Regtest mode with specific RPC credentials.     |
| `fulcrum-config.conf` | Configures the **Fulcrum** Electrum server to index your local Regtest node.          |
| `compose.yml`         | A Docker Compose file to spin up the **Mempool** block explorer backend and frontend. |
| `ord.yaml`            | Configuration for the **Ord** tool to experiment with inscriptions locally.           |

## Prerequisites

You will need the following software installed:

1. **[Bitcoin Core](https://bitcoin.org/en/download)** (Latest stable release)
2. **[Fulcrum](https://github.com/cculianu/Fulcrum/releases)** (Latest release)
3. **[Docker Desktop](https://docs.docker.com/engine/install/)** (For running Mempool)
4. **[Ord](https://github.com/ordinals/ord/releases)** (Optional, for inscriptions)

## Important Configuration Note

The configuration files in this repository use **Windows file paths** (e.g., `D:\Program\Bitcoin\data`) as placeholders.

> [!TIP]  
> I don't know if this would matter, but if you encounter issues with the configuration file, perhaps check that the encoding and EOF is correct for your OS?

**Before running anything:**

1. Open each `.conf` and `.yaml` file.
2. Search for `datadir` or directory paths.
3. Update them to match the actual directory structure on your local machine (Mac/Linux users must switch to forward slashes `/`).

## Usage

### 1. Bitcoin Core

Place `bitcoin.conf` in a known location. Start Bitcoin Core pointing to this config:

```bash
./bitcoin-qt -conf="/path/to/bitcoin.conf"
```

- **RPC User**: `mempool`
- **RPC Password**: `mempool`

### 2. Fulcrum Server

Update fulcrum-config.conf to point to your Bitcoin data directory. Start Fulcrum:

```bash
./Fulcrum /path/to/fulcrum-config.conf
```

- **Note**: You must mine at least 1 block (`generatetoaddress 1 <addr>`) for Fulcrum to initialize successfully.

### 3. Mempool Explorer

Ensure Docker is running. Navigate to the folder containing `compose.yml` and run:

```bash
docker compose up -d
```

Access the explorer at: <http://localhost:8089>

### 4. Ord (Optional)

Update `ord.yaml` with your paths. Run:

```bash
./ord --config ord.yaml server --http-port 8088
```

### Startup Sequence

Because these services depend on one another, you should always start them in the following order:

- **Start Bitcoin Core** - Wait for RPC initialization.
- **Start Fulcrum** - Wait for it to sync with the tip.
- **Start Mempool** - Wait for the web interface to load.
- **Start Ord** - Can be started anytime after step 1.

## License

[MIT](./LICENSE)
