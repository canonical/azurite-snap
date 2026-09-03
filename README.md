# Azurite Snap

A [snap](https://snapcraft.io/) package for [Azurite](https://github.com/Azure/Azurite),
the open-source Azure Storage API compatible server (emulator). It lets you run
a local instance of the Azure **Blob**, **Queue**, and **Table** (preview)
services.

The snap bundles its own Node.js 22 runtime and runs under `strict`
confinement.

## Project layout

```
azurite-snap/
├── snap/snapcraft.yaml     # snap recipe (core24, strict confinement)
├── package.json            # wrapper manifest used to bundle the Node.js runtime
└── scripts/                # launch wrappers (persist data in $SNAP_USER_COMMON)
    ├── run-azurite
    ├── run-azurite-blob
    ├── run-azurite-queue
    └── run-azurite-table
```

## Build

Requires `snapcraft` and a build backend (LXD).

```bash
cd azurite-snap
snapcraft pack
```

This produces `azurite_3.37.0_amd64.snap`.

## Install

```bash
sudo snap install ./azurite_3.37.0_amd64.snap --dangerous
```

The `network`, `network-bind`, and `home` interfaces auto-connect on install.

## Usage

The snap exposes the following commands:

| Command          | Description                                    |
|------------------|------------------------------------------------|
| `azurite`        | Run the Blob, Queue and Table services together |
| `azurite.blob`   | Run only the Blob service                       |
| `azurite.queue`  | Run only the Queue service                      |
| `azurite.table`  | Run only the Table service                      |

Examples:

```bash
# Start only the local Blob service on the default port (10000)
azurite.blob

# Start all services, listening on all interfaces
azurite --blobHost 0.0.0.0 --queueHost 0.0.0.0 --tableHost 0.0.0.0
```

By default, data is persisted under the snap's per-user common directory
(`~/snap/azurite/common`). Any extra Azurite command-line options are passed
through, e.g. `-l <path>` to change the workspace location (requires the `home`
interface for paths inside your home directory).

### Default endpoints & credentials

- Blob:  `http://127.0.0.1:10000/devstoreaccount1`
- Queue: `http://127.0.0.1:10001/devstoreaccount1`
- Table: `http://127.0.0.1:10002/devstoreaccount1`

Default account (same as the Azure Storage Emulator):

- Account name: `devstoreaccount1`
- Account key: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

Short connection string for SDKs/tools: `UseDevelopmentStorage=true;`

## Uninstall

```bash
sudo snap remove --purge azurite
```
