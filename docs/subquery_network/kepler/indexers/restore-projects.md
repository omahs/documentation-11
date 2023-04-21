# Restore SubQuery Projects

## Introduction

Before you providing indexing services on SubQuery Network, you need to have the project that you indexing fully synced with POI enabled. Using a data snapshot we provided to sync a project, it can dramatically reduce the amount of time needed to index the project and achieve a 100% sync.


| PROJECT NAME | BLOCKHEIGHT | SIZE | S3 URL |DEPLOYMENT CID                         | 
| ------------ | ----------- | ---- | ------ |-------------------------------------- | 
| POLKADOT     | 15154676    | 13G  |        |QmRRv6VS1bDdjpUu83NNfWsgw6qo89vRptmAPMPoZC8Xh1 |
| KUSAMA       |             |      |        |QmXwfCF8858YY924VHgNLsxRQfBLosVbB31mygRLhgJbWn |
| MOONBEAN     |             |      |        |QmRwisx41SRrR8fhNTRpKetNdpP7SaNkRAVrRgcdwoEtCH |

## POLKADOT

### Step 1 - Download snapshot from s3 bucket

```batch
curl -o polkadot.dictionary.tar s3_url
```

### Step 2 - Prepare the data

```batch
tar -xvf polkadot.dictionary.tar
```

You will get 2 files: 
- `.mmr` 
- `schema_qmrrv6vs1bddjpu.dump`

### Step 3 - Restore MMR

Move .mmr to  configed mmrPath
```batch 
docker cp .mmr <indexer_coordinator_container_id>:<mmrPath>/poi/QmRRv6VS1bDdjpUu83NNfWsgw6qo89vRptmAPMPoZC8Xh1
```

::: warning Important
1. `mmrPath` is the path when you config coordinator_container
2. Use `docker ps` to view listing which includes container_ids
:::

### Step 4 - Restore indexing data

schema_qmrrv6vs1bddjpu.dump is the binary file for project data. This will be used to restore the indexing data to your db

Make sure your indexer_db and indexer_coordinator containers are running with healthy status

```batch
docker cp schema_qmrrv6vs1bddjpu.dump <indexer_db_container_id>:.data/postgres/

docker exec -it indexer_db pg_restore -v -j 2 -h localhost -p 5432 -U postgres -d postgres /var/lib/postgresql/data/<dump_file.dump>
```

here is an example of the output log:

```
pg_restore: creating SCHEMA "schema_qmzj9whrhrmvn2h"
pg_restore: creating FUNCTION "schema_qmzj9whrhrmvn2h.schema_notification()"
pg_restore: creating TABLE "schema_qmzj9whrhrmvn2h._metadata"
pg_restore: creating TABLE "schema_qmzj9whrhrmvn2h._poi"
pg_restore: creating TABLE "schema_qmzj9whrhrmvn2h.events"
pg_restore: creating TABLE "schema_qmzj9whrhrmvn2h.extrinsics"
pg_restore: creating TABLE "schema_qmzj9whrhrmvn2h.spec_versions"
pg_restore: processing data for table "schema_qmzj9whrhrmvn2h._metadata"
pg_restore: processing data for table "schema_qmzj9whrhrmvn2h._poi"
pg_restore: processing data for table "schema_qmzj9whrhrmvn2h.events"
pg_restore: processing data for table "schema_qmzj9whrhrmvn2h.extrinsics"
pg_restore: processing data for table "schema_qmzj9whrhrmvn2h.spec_versions"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h._metadata _metadata_pkey"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h._poi _poi_chainBlockHash_key"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h._poi _poi_hash_key"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h._poi _poi_mmrRoot_key"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h._poi _poi_parentHash_key"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h._poi _poi_pkey"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h.events events_pkey"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h.extrinsics extrinsics_pkey"
pg_restore: creating CONSTRAINT "schema_qmzj9whrhrmvn2h.spec_versions spec_versions_pkey"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.events_block_height__block_range"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.events_event__block_range"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.events_module__block_range"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.extrinsics_block_height__block_range"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.extrinsics_call__block_range"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.extrinsics_module__block_range"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.extrinsics_tx_hash__block_range"
pg_restore: creating INDEX "schema_qmzj9whrhrmvn2h.poi_hash"
pg_restore: creating TRIGGER "schema_qmzj9whrhrmvn2h._metadata 0xc1aaf8b4176d0f02"
```

## Additional Notes
It takes some time to restore the data:
| PROJECT NAME | RESTORE TIME COST                         | 
| ------------ | -------------------------------------- | 
| POLKADOT     | ~2 DAYS |
| KUSAMA       | ~2 DAYS |
| MOONBEAN     | ~1.5 DAYS |