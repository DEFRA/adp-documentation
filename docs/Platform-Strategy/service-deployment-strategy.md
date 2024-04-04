---
title: Service Deployment Strategy
summary: 
uri: https://defra.github.io/adp-documentation/Platform-Strategy/service-deployment-strategy/
authors:
    - Dan Rozkowski
date: 2024-04-04
---

# Platform service Deployment Strategy

## Guidance and Context

This article outlines the Platform service deployment strategies available. Development teams should read the Platform Versioning and Git strategy document before reading this. ADP’s primary deployment strategy is **Rolling Deployments** on [AKS](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/) with [HELM](https://atlassian.github.io/data-center-helm-charts/userguide/upgrades/HELM_CHART_UPGRADE/#3-define-the-upgrade-method) and [FluxCD](https://fluxcd.io/flagger/usage/deployment-strategies/). This provides Platform services with a zero-downtime deployment strategy. This allows applications to achieve high availability with low/no business impact to live service. This is important for services that need 24/7 availability and allows the capability to deploy to production multiple times a day. In the future, we will support other deployment strategies, such as Blue-Green and Canary deployments.

## Deployment Strategies - ADP Rolling Updates

ADP uses AKS (Kubernetes) with [HELM Charts](https://helm.sh/docs/topics/charts/) and Flux to perform rolling deployments. The default strategy applied to all services is rolling deployments, unless otherwise specified in the deployment YAML. We recommend starting with this strategy. This strategy allows for applications to be incrementally updated without downtime.
There are 3 core parts to a Service deployment/upgrade, which are done in the following order:

1.	App Configuration, including Secrets,
2.	Service Infrastructure,
3.	Database upgrade and Web Application

The deployment process flow:

1.	A new deployment is triggered via the [CI & CD Pipelines](https://github.com/DEFRA/ado-pipeline-common?tab=readme-ov-file) for the Service:
    1. New app Secrets are imported/updated/deleted* in the Key Vault and are mastered in the Azure DevOps (ADO) Secret Library Groups for the service.
    2. New App Configuration keys and values are imported/updated/deleted in the Service Config Maps & App Configuration Service from the Service’s ‘appConfig.yaml’ files. Note: The sentinel key is not updated yet.
2. The new images and artefact are pushed to the environment Container Registry (ACR) (via pipeline deployment) and Flux updates the [Services repository](https://github.com/DEFRA/adp-flux-services) with the new version to be deployed:
   1. This can be a higher version (new image & release) or lower version (existing/rollback).

3. Flux [reconciles](https://learn.microsoft.com/en-us/azure/azure-arc/kubernetes/tutorial-use-gitops-flux2?tabs=azure-cli#apply-a-flux-configuration) the Cluster with the new Web App code and Infrastructure versions requested with a rolling update. Any infrastructure updates take precedence over Application (Infra > App).
   **Application deployment:**

   1. The deployment will incrementally add new Pods (web applications) onto the Nodes in the Cluster. This will automatically pick up the new App Config/Secret updates on startup.
   2. AKS deployment will wait for those new Pods (apps) to start successfully with the configured/default (5m) wait times and health check endpoints.
   3. Once the new pods are started and reporting healthy via the endpoint(s), traffic will then be directed to the new Pods (updated app) via the internal load balancer/NGINX gracefully.
   4. The old Pods (previous version) will be deleted incrementally if the new Pods have started successfully, and all traffic has drained gracefully.
   5. If the new App/Pod does not start successfully, the deployment will time out and fail after a set period of health check retries (5m), but the previous app version (Pods) will remain in place and accepting traffic. The previous version’s App Config will remain as-is/unchanged on the none-upgraded Pods.
      1. Unhealthy Pods will be removed if an upgrade fails.

   **Infrastructure deployment:**

   6. The new infrastructure will be deployed (created, updated, or deleted). This can be Queues, Topics, Datastores, Identities, etc.
   7. Once the infrastructure upgrade is successful, the App (and database If applicable) can be deployed/upgraded.

   **Database deployment:**

   8. If a new DB Schema is to be deployed (migration required), this will be done before the Web Application is deployed. 
   9. [Liquibase](https://docs.liquibase.com/start/tutorials/postgresql/postgresql-azure-database.html) will perform the PostgreSQL migration using a [Flux pre-deploy](https://fluxcd.io/flux/use-cases/running-jobs/) job.
   10. If database deployment/migration fails, the App will not be upgraded.

4. If a user has requested the deployment of App Config/Secrets only via the [Flag in the build.yaml](https://github.com/DEFRA/ado-pipeline-common/blob/main/docs/AppBuildAndDeploy.md#buildyaml-for-nodejs-app), the App or Infra will not be deployed on this release:
a.	The App Config & Secrets will be updated via the Pipeline, including the Sentinel Key with the Build ID – which triggers the configuration update.
b.	The [Reloader](https://github.com/stakater/Reloader) service will perform a rolling and zero-downtime [upgrade](https://github.com/stakater/Reloader?tab=readme-ov-file#problem) (restart Pods) of the Service to consume the new App configuration (incremental Pod restarts).

!!! note
    All releases / deployments are promoted via the Common CI and CD Pipelines using Azure DevOps as the orchestrator. We promote continuous delivery with automated checks and tests above/in preference to manual intervention and approvals. Approval gates can be added optionally to Azure Pipelines to gate the promotion of code. 

## Deployment and App Configuration Guidance / Context

**All services will have the following settings defaulted (changeable if required):**

- maxSurage – maximum additional Pods created at one time (50%).
- maxUnavailable – max Pods not available (25%)
- podDisruptionBudget – allowed disruptions for a Pod (application) (25% or at least 1)
- min and max replicas – number of replicas of the application in the Cluster. Minimum of 3 for production for high availability. 
- All deployments of business apps are on the User/Apps Node Pools. Platform/System apps are on the System Node Pool. Taints/tolerations applied to that effect.
- Autoscaling via HPA is enabled.
- All services will have their own dedicated AKS Namespaces for their own team.

**Constraints**

- Infrastructure is always deployed first if changed; database Schema migrations are second and App code is last (associated Config & Secrets consumed at that point)
- Database updates, if using PostgreSQL, will require development teams to deploy non-breaking changes and/or manage their schema updates appropriately with their app deployment to prevent downtime.
  - Shutter pages will be included in phase 2 / Post MVP if required.
- Development teams must set health endpoints correctly for an effective rolling update.
- App Config, Infrastructure and Application Code are tied (versioned) together as an immutable unit.
  - They are versioned using semver strategy defined in the versioning article.
- App Secrets in Key Vault/ADO library group are not versioned with the App/Code or Infra, they are fully independent, and can be rotated periodically. 
  - All secret rotations must have an overlap in expiry periods to ensure zero-downtime upgrades. Secrets should not be tied to versions as they are rotatable as good practice.
- The Platform has defined minimum replicas/availability to meet Defra SLA’s.
- The Platform **Reloader** Service will drain and replace the Pods in the Cluster with a rolling upgrade on detection of new App Config or New App Secrets automatically via the Sentinel Key update.
- All HELM Deployments are full CRUD operations – add, update, or delete. This includes Apps, Infra and Databases. Warning: You can delete your own infrastructure and configuration!
- All App Configuration updates are full CRUD operations – create, update, or delete.
- Secrets are add/update only for MVP. *Delete will be added post-MVP.
