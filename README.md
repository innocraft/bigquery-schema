# Matomo BigQuery schemas

This repository contains JSON schema definitions for Matomo tables. The schemas are used to initialize Google BigQuery datasets that receive JSONL export data from the Matomo Data Warehouse Connector.

Although these files are directly compatible with BigQuery schema JSON files, they are not intended to be limited to BigQuery. They can also be used as a structured reference for creating equivalent tables in other data warehouses.

## Schema files

Each `*.json` file represents one Matomo table. The filename without the `.json` extension is the expected table name.

For example:

- `log_visit.json` defines the `log_visit` table.
- `log_link_visit_action.json` defines the `log_link_visit_action` table.
- `site.json` defines the `site` table.

## Initialize tables in BigQuery

Install and authenticate the Google Cloud CLI, then select the project that should contain the Matomo export dataset:

```bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```

Create a BigQuery dataset:

```bash
bq mk --dataset --location=US YOUR_PROJECT_ID:matomo
```

Create one BigQuery table for each schema file:

```bash
PROJECT_ID="YOUR_PROJECT_ID"
DATASET="matomo"

for schema in *.json; do
  table="${schema%.json}"
  bq mk \
    --table \
    "${PROJECT_ID}:${DATASET}.${table}" \
    "${schema}"
done
```

If your dataset already exists and you only need to add a single table, run:

```bash
bq mk --table YOUR_PROJECT_ID:matomo.log_visit log_visit.json
```


## Releases

Pushing a tag that starts with `v` creates a GitHub release containing `bigquery-json-schemas.zip`, an archive of all schema JSON files in this repository.

For example:

```bash
git tag v0.0.0-test.1
git push origin v0.0.0-test.1
```

Tags containing a hyphen, such as `v0.0.0-test.1`, are published as GitHub prereleases.
