---
title: How to delegate database access between microservices
summary: How to delegate database access between microservices hosted in ADP.
uri: https://defra.github.io/adp-documentation/How-to-guides/Platform-Services/how-to-delegate-db-access-between-services/
authors:
  - Jeevan Kuduva Ravindran
date: 2024-08-14
weight: 2
---

# How to delegate database access between microservices - Draft version

## Overview

This documentation outlines a proposed design for enabling shared access for databases between microservices.

## Considerations

- Both microservices which share the database need to be in the same namespace
- The delegated microservice will only have read/write access

## Proposed Design

A k8s job template will be added to the application helmchart which can execute a powershell script to validate and provide the necessary permissions to the delegatee service.

- Delegator (Microservice that owns the database)
- Delegatee (Microservice that requests access to the database)

Delegator service needs to mark the database as delegatable and which microservices can access the database.

### Helm library

A new k8s Job template will be added to [adp-aso-helm-library](https://github.com/DEFRA/adp-aso-helm-library) to run the postgresql script to create the role and provide necessary permissions for delegatee service in the target database. By default, the role is created for a service that owns a database and it is part of flux pre-deploy kustomization.

Database template on the library will also need to be modified to handle the conditional creation/delegation of database with appropriate flags.

#### Job template

The k8s job uses the existing pattern of running a powershell script inside a docker container. This job will run the provided script that will validate and grant the access to delegatee service.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: delegate-db-access
  namespace: flux-config
  labels:
    azure.workload.identity/use: "true"
    backstage.io/kubernetes-id: <<service-name>>
    backstage.io/kubernetes-team: <<namespace>>
spec:
  template:
    metadata:
      labels:
        azure.workload.identity/use: "true"
        backstage.io/kubernetes-id: <<service-name>>
        backstage.io/kubernetes-team: <<namespace>>
    spec:
      serviceAccountName: <<service-account-for-workload-id>>
      restartPolicy: Never
      volumes:
      - name: custom-root-ca
        secret:
          secretName: custom-ca-trust-secret
          optional: true
      containers:
      - name: <<service-name>>-delegate-db-access
        imagePullPolicy: Always
        image: <<acr-name>>.azurecr.io/image/powershell-executor:491667
        env:
        - name: GIT_REPO_URL
          value: https://github.com/DEFRA/adp-flux-services.git
        - name: GIT_BRANCH
          value: main
        - name: SCRIPT_FILE_NAME
          value: common/scripts/access-control/flexible-server/<<powershell-script-filename>>
        - name: POSTGRES_HOST
          value: <<postgres-server>>.postgres.database.azure.com
        - name: POSTGRES_DATABASE
          value: <<postgres-db>>
        ...
        resources:
          requests:
            cpu: 100m
            memory: 250Mi
          limits:
            cpu: 500m
            memory: 600Mi
        volumeMounts:
        - name: custom-root-ca
          readOnly: true
          mountPath: /etc/ssl/certs/defra-egress-firewall-cert-01.crt
          subPath: defra-egress-firewall-cert-01.crt
  backoffLimit: 2
```

### Microservice Infra Helm Chart (TBD)

The above job template can be easily included in the application helm chart templates with the below code.

```yaml
{ { - include "adp-aso-helm-library.delegate-db-access" . - } }
```

In the `values.yaml` for infra helm chart, the delegator service would add a label/flag to denote that the db can be provided delegate permissions. By default, delegate will be set to `'no'`.

```yaml
postgres:
  db:
    name: claim
    delegate: 'yes'             --> optional
    charset: UTF8
    collation: en_US.utf8
```

On the other hand, the delegatee service would have the `delegated` parameter set to `'yes'`. Setting `delegated` parameter ensures that a new database won't be created and it will provide roleassignments on the existing database.

This will require `roleassignment` to be set to `read` or `readwrite` and ignore any other parameters set for the database in values.yaml.

```yaml
postgres:
  db:
    name: claim
    delegated: 'yes'
    roleAssignments:
      - roleName: 'readwrite'   --> required if delegated is set to 'yes'
```

### Job - Powershell script (TBD)

- Validate: The powershell script needs to validate the below before providing necessary access to the database.
  - The database should have been set to `delegate` by the delegator service to enable sharing access.
  - Check if the delegatee service name is listed by the delegator service to share access to db.

- Grant access: Runs a set of SQL scripts on the `postgres` db with the team's `postgres-db-writer AD group` to create delegatee service role and grant necessary access.

#### Validate

Powershell script to check if the database is delegatable and the service is authorized by delegator service to allow granting access.

#### Grant Access

##### Create Role

```sql
DO $$
BEGIN
    IF NOT EXISTS (SELECT 1 FROM pgaadauth_list_principals(false) WHERE rolname='<<delegatee-service-MI-name>>') THEN
        RAISE NOTICE 'CREATING PRINCIPAL FOR MANAGED IDENTITY:<<delegatee-service-MI-name>>';
        PERFORM pgaadauth_create_principal('<<delegatee-service-MI-name>>', false, false);
        RAISE NOTICE 'PRINCIPAL FOR MANAGED IDENTITY CREATED:<<delegatee-service-MI-name>>';
    ELSE
        RAISE NOTICE 'PRINCIPAL FOR MANAGED IDENTITY ALREADY EXISTS:<<delegatee-service-MI-name>>';
    END IF;

    EXECUTE ('GRANT CONNECT ON DATABASE "crai-mcu-knowledge" TO "<<delegatee-service-MI-name>>"' );
    RAISE NOTICE 'GRANTED CONNECT TO DATABASE';

EXCEPTION
    WHEN OTHERS THEN
        RAISE EXCEPTION 'ERROR DURING PRINCIPAL CREATION/GRANT CONNECT: %', SQLERRM;
END $$
```

##### Reader perms

```sql
GRANT SELECT ON ALL TABLES IN SCHEMA public TO "<<delegatee-service-MI-name>>";
GRANT SELECT ON ALL SEQUENCES IN SCHEMA public TO "<<delegatee-service-MI-name>>";
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO "<<delegatee-service-MI-name>>";
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON SEQUENCES TO "<<delegatee-service-MI-name>>";
```

##### Writer perms

```sql
GRANT CREATE, USAGE ON SCHEMA public TO "<<delegatee-service-MI-name>>";
GRANT SELECT, UPDATE, INSERT, REFERENCES, TRIGGER ON ALL TABLES IN SCHEMA public TO "<<delegatee-service-MI-name>>";
GRANT SELECT, UPDATE, USAGE ON ALL SEQUENCES IN SCHEMA public TO "<<delegatee-service-MI-name>>";
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA public TO "<<delegatee-service-MI-name>>";
GRANT EXECUTE ON ALL PROCEDURES IN SCHEMA public TO "<<delegatee-service-MI-name>>";
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, UPDATE, INSERT, REFERENCES, TRIGGER ON TABLES TO "<<delegatee-service-MI-name>>";
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, USAGE ON SEQUENCES TO "<<delegatee-service-MI-name>>";
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT EXECUTE ON FUNCTIONS TO "<<delegatee-service-MI-name>>";
```
