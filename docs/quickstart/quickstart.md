# 1. Create a New Project

The goal of this quick start guide is to provide you with a complete development setup and guided steps to create your first SubQuery blockchain project. It is targeted towards experienced developers right through to those just beginning their blockchain journey.

This quick start guide should take around 10-15 minutes.

After completing this quick start guide, you will have a working SubQuery project that will run on a SubQuery node. You will be able to adapt the standard starter project and index transfers from your favourite blockchain network such as Polkadot, Avalanche, Cosmos, etc.

Let's start the process of creating your first SubQuery blockchain project.

## Prerequisites

Before you begin creating your first blockchain project with SubQuery, make sure you have installed the required supporting software applications. These are:

- [Node](https://nodejs.org/en/): A modern (e.g. the LTS version) installation of Node.
- [Docker](https://docker.com/): This tutorial will use the required Docker.

Now, you are all set to start with the first step, which is the installation of the SubQuery CLI.

## 1. Install the SubQuery CLI

Install SubQuery CLI globally on your terminal by using NPM:

```shell
# NPM
npm install -g @subql/cli
```

::: danger
We **DO NOT** encourage the use of `yarn global` for installing `@subql/cli` due to its poor dependency management. This may lead to multiple errors.
:::

Take a look at all available commands and their use. Run the given below command in the CLI:

```shell
subql help
```

## 2. Initialise the SubQuery Starter Project

Run the following command inside the directory you want to create a SubQuery project:

```shell
subql init
```

You'll be asked certain questions as you proceed ahead:

- **Project name**: A project name for your SubQuery project.
- **Network family**: The layer-1 blockchain network family that this SubQuery project will index. Use the arrow keys to select from the available options. For example, Polkadot, Avalanche, Cosmos, or any other supported network.
- **Network**: The specific network that this SubQuery project will index. Use the arrow keys to select from the available options. For example, Polkadot, Avalanche, or any other supported network.
- **Template project**: Select a SubQuery template project that will provide a starting point in the development. We suggest selecting the _"subql-starter"_ project.
- **RPC endpoint**: Provide an HTTP or websocket URL to a running RPC endpoint, which will be used by default for this project. You can quickly access public endpoints for different networks, create your own private dedicated node using [OnFinality](https://app.onfinality.io), or just use the default endpoint. This RPC node must have the entire state of the data that you wish to index, so we recommend an archive node. We will use the default value for this guide. Based on the network you have chosen, the default value may be:
  - For Polkadot - _"wss://polkadot.api.onfinality.io/public-ws"_,
  - For Avalanche - _"https://avalanche.api.onfinality.io/public/ext/bc/C/rpc"_,
  - For Ethereum - _“https://eth.api.onfinality.io/public”_ and likewise for other networks.
- **Git repository**: Provide a Git URL to a repo that this SubQuery project will be hosted in (when hosted in SubQuery Explorer) or accept the provided default.
- **Authors**: Enter the owner of this SubQuery project here (e.g. your name!) or accept the provided default.
- **Description**: Provide a short paragraph about your project that describes what data it contains and what users can do with it, or accept the provided default.
- **Version**: Enter a custom version number or use the default (`1.0.0`).
- **License**: Provide the software license for this project or accept the default (`MIT`).

Let’s look at an example:

```shell
$ subql init
Project name [subql-starter]: HelloWorld
? Select a network family Substrate
? Select a network Polkadot
? Select a template project subql-starter     Starter project for subquery
RPC endpoint: [wss://polkadot.api.onfinality.io/public-ws]:
Git repository [https://github.com/subquery/subql-starter]:
Fetching network genesis hash... done
Author [Ian He & Jay Ji]: Sean
Description [This project can be used as a starting po...]:
Version [1.0.0]:
License [MIT]:
Preparing project... done
HelloWorld is ready
```

After you complete the initialisation process, you will see a folder with your project name created inside the directory. Please note that the contents of this directory should be identical to what's listed in the [Directory Structure](../build/introduction.md#directory-structure).

Finally, run the following command to install the new project’s dependencies from within the new project's directory.

::: code-tabs
@tab:active yarn

```shell
cd PROJECT_NAME
yarn install
```

@tab npm

```shell
cd PROJECT_NAME
npm install
```

:::

You have now initialised your first SubQuery project with just a few simple steps. Let’s now customise the standard template project for a specific blockchain of interest.

You may want to refer to the [command line arguments](../run_publish/references.md) used in SubQuery. It will help you understand the commands better.

## 3. Make Changes to Your Project

There are 3 important files that need to be modified. These are:

1. The GraphQL Schema in `schema.graphql`.
2. The Project Manifest in `project.yaml`.
3. The Mapping functions in `src/mappings/` directory.

SubQuery supports various blockchain networks and provides a dedicated guide for each of them. Select your preferred blockchain under **2. Specific Chains** and continue the quick start guide.
