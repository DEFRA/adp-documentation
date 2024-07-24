---
title: How to create a database
summary: How to create a database for platform service hosted on the ADP.
uri: https://defra.github.io/adp-documentation/How-to-guides/Platform-Services/how-to-create-a-database/
authors:
  - Ken Babigumira
date: 2024-07-24
weight: 2
---

# How to create a database for a platform service

[PostgreSQL](https://www.postgresql.org/) is the preferred relational database for microservices.  This guide describes the process for creating a database for a microservice and configuring the microservice to use it.

!!! note
    The ADP Platform currently supports PostgreSQL only as the option available for a relational database.

## How to create a Postgres Database

There are two ways for creating a Postgres Database in the ADP.

1. When scaffolding a new Backend service using the ADP Portal. You have the option to specify the name of the database. Refer to the section on [Selecting a template](how-to-create-a-platform-service.md#selecting-a-template)

2. For an existing service, you can add values to the Infrastructure Helm Chart values. Refer to Infrastructure section on [Database for Flexible Server](../../Developer-Reference/Infrastructure/ASO%20Helm%20Library%20Chart.md#database-for-postgres-flexible-server-template)

!!! tip
    An example of how to specify the Helm Chart values is provided in the [ffc-demo-claim-service repository](https://github.com/DEFRA/ffc-demo-claim-service/), refer to the configuration in the [values.yaml](https://github.com/DEFRA/ffc-demo-claim-service/blob/master/helm/ffc-demo-claim-service-infra/values.yaml#L16).

## How to appy Database migrations

The ADP Platform CI and deployment pipelines support database migrations using [Liquibase](https://www.liquibase.org/).

Create a Liquibase changelog defining the structure of your database available from the root of your microservice repository in `changelog/db.changelog.xml`.

Guidance on creating a Liquibase changelog is outside of the scope of this guide.

## Update Docker Compose files to use Postgres service and environment variables

Update `docker-compose.yaml`, `docker-compose.override.yaml`, and `docker-compose.test.yaml` to include a Postgres service and add Postgres environment variables to the microservice.

Replace `<programme code>` and `<service>` as per naming convention described above.

## Local Development

The following scripts and files are scaffolded as part of your backend service to provide a good local development experience.

- A `docker-compose.migrate.yaml` in the root of your microservice repository that spins up Postgres in a Docker container.
- The `scripts/` folder contains three bash scripts `start`, `test` and `postgres-wait`.
- The `scripts/migration/` folder contains two scripts to apply and remove migrations. The two scripts are `database-up` and `database-up`.

Execute the `start` script to start the Postgres container.

```sh
# snippet of the code in the start script

cd "${projectRoot}"
# Guarantee clean environment
docker-compose down -v
docker-compose -f docker-compose.migrate.yaml down -v
# Ensure container images are up to date
docker-compose -f docker-compose.migrate.yaml run database-up
docker-compose up --build

```

## How to Enable POSTGRES Extensions

Some microservices require Postgres extensions to be installed in the database. Below is the list of the enabled extensions:

- VECTOR
- UUID-OSSP

This is a two step process.

### Step 1. Enable extensions on the server

!!! tip
    Request the ADP Platform Team to enable the extension on the Postgres Flexible server if it is not in the list above of enabled extensions.

### Step 2. Enable extensions on the database using a user account that has database admin permissions

   When scripting the database migrations for creating extensions, use `IF NOT EXISTS`. This will ensure that the scripts can both run locally and an in Azure. 

   When running the Postgres database locally in Docker, you will have sufficient permissions to create the extensions. However, in Azure, the ADP Platform will apply the migrations to the database instead of using the microservice's managed identity. If you don't use  `IF NOT EXISTS`, the migrations on the Azure Postgres database will fail due to insufficient permissions.

   Below is an example of a SQL script you can use in your migration to enable an extension.

   ```sql

    CREATE EXTENSION IF NOT EXISTS vector;
    CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

   ```
