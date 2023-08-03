# Stellar Mapping

Mapping functions define how chain data is transformed into the optimised GraphQL entities that we have previously defined in the `schema.graphql` file.

- Mappings are defined in the `src/mappings` directory and are exported as a function.
- These mappings are also exported in `src/index.ts`.
- The mappings files are reference in `project.yaml` under the mapping handlers.

There are different classes of mappings functions for Stellar: [Block handlers](#block-handler), [Transaction Handlers](#transaction-handler), [Operation Handlers](#operation-handler), [Effect Handlers](#effect-handler).

## Block Handler

You can use block handlers to capture information each time a new block is attached to the chain, e.g. block number. To achieve this, a defined BlockHandler will be called once for every block.

**Using block handlers slows your project down as they can be executed with each and every block - only use if you need to.**

```ts
import { SorobanBlock } from "@subql/types-soroban";

export async function handleBlock(block: SorobanBlock): Promise<void> {
  const _block = Ledger.create({
    id: block.id,
    sequence: BigInt(block.sequence),
    hash: block.hash
  })

  await _block.save();
}
```

## Transaction Handler

You can use transaction handlers to capture information about each of the transactions in a block. To achieve this, a defined TransactionHandler will be called once for every transaction. You should use [Mapping Filters](../manifest/stellar.md#mapping-handlers-and-filters) in your manifest to filter transactions to reduce the time it takes to index data and improve mapping performance.

```ts
import { SorobanTransaction } from "@subql/types-soroban";

export async function handleTransaction(
  tx: SorobanTransaction
): Promise<void> {
  const _tx = Transaction.create({
    id: tx.id,
    source_account: tx.source_account,
    operation_count: tx.operation_count
  })

  await _tx.save();
}
```

## Operation Handler

You can use operation handlers to capture information about each of the operation in a block. To achieve this, a defined OperationHandler will be called once for every operation. You should use [Mapping Filters](../manifest/stellar.md#mapping-handlers-and-filters) in your manifest to filter operation to reduce the time it takes to index data and improve mapping performance.

```ts
import { SorobanOperation } from "@subql/types-soroban";

export async function handleOperation(
  tx: SorobanOperation
): Promise<void> {
  const _op = Operation.create({
    id: op.id,
    account: op.source_account,
    type: op.type,
    txHash: op.transaction_hash
  })

  await _op.save();
}
```

## Effect Handler

You can use operation handlers to capture information about each of the operation in a block. To achieve this, a defined OperationHandler will be called once for every operation. You should use [Mapping Filters](../manifest/stellar.md#mapping-handlers-and-filters) in your manifest to filter operation to reduce the time it takes to index data and improve mapping performance.

```ts
import { SorobanEffect } from "@subql/types-soroban";

export async function handleEffect(
  tx: SorobanEffect
): Promise<void> {
  const _effect = Effect.create({
    id: effect.id,
    account: effect.account,
    type: effect.type
  })

  await _effect.save();
}
```



## Third-party Library Support - the Sandbox

SubQuery is deterministic by design, that means that each SubQuery project is guaranteed to index the same data set. This is a critical factor that is required to decentralise SubQuery in the SubQuery Network. This limitation means that in default configuration, the indexer is by default run in a strict virtual machine, with access to a strict number of third party libraries.

**You can easily bypass this limitation however, allowing you to retrieve data from external API endpoints, non historical RPC calls, and import your own external libraries into your projects.** In order to do to, you must run your project in `unsafe` mode, you can read more about this in the [references](../../run_publish/references.md#unsafe-node-service). An easy way to do this while developing (and running in Docker) is to add the following line to your `docker-compose.yml`:

```yml
subquery-node:
  image: onfinality/subql-node-soroban:latest
  ...
  command:
    - -f=/app
    - --db-schema=app
    - --unsafe
  ...
```

When run in `unsafe` mode, you can import any custom libraries into your project and make external API calls using tools like node-fetch. A simple example is given below:

```ts
import { SorobanTransaction } from "@subql/types-soroban";
import fetch from "node-fetch";

export async function handleTransaction(
  tx: SorobanTransaction
): Promise<void> {
  const httpData = await fetch("https://api.github.com/users/github");
  logger.info(`httpData: ${JSON.stringify(httpData.body)}`);
  // Do something with this data
}
```

By default (when in safe mode), the [VM2](https://www.npmjs.com/package/vm2) sandbox only allows the following:

- only some certain built-in modules, e.g. `assert`, `buffer`, `crypto`,`util` and `path`
- third-party libraries written by _CommonJS_.
- external `HTTP` and `WebSocket` connections are forbidden

## Modules and Libraries

To improve SubQuery's data processing capabilities, we have allowed some of the NodeJS's built-in modules for running mapping functions in the [sandbox](#third-party-library-support---the-sandbox), and have allowed users to call third-party libraries.

Please note this is an **experimental feature** and you may encounter bugs or issues that may negatively impact your mapping functions. Please report any bugs you find by creating an issue in [GitHub](https://github.com/subquery/subql).

### Built-in modules

Currently, we allow the following NodeJS modules: `assert`, `buffer`, `crypto`, `util`, and `path`.

Rather than importing the whole module, we recommend only importing the required method(s) that you need. Some methods in these modules may have dependencies that are unsupported and will fail on import.

```ts
import { hashMessage } from "ethers/lib/utils"; // Good way
import { utils } from "ethers"; // Bad way
```
