---
title: Service Versioning Strategy
summary: Platform service versioning strategy.
uri: https://defra.github.io/adp-documentation/Platform-Strategy/service-versioning-strategy/
authors:
    - Dan Rozkowski
date: 2024-03-14
---

# Platform service versioning strategy.

This article outlines a two-phase versioning strategy for services on ADP with the goal to support ephemeral environments by phase 2. 

**The following Git and Versioning strategies are in place and mandated:**

- A [Sematic Versioning](https://semver.org/) (SemVer) strategy for all Platform and business services (app code and infrastructure)
- The [Trunk Based Development Git](https://trunkbaseddevelopment.com/) strategy for application development (code and infrastructure).

In Phase 1, before ephemeral environments, Feature branch builds fetch the version from the main branch’s **package.json** file for Node and the ***.csproj** file for C#. If the versions are the same, a validation error is thrown; if the feature branch version is higher, it's tagged with ‘**-alpha**’ and the pipeline build ID. When the main branch version is pushed to the ACR on deployment after merging into main, it will take precedence over all feature (alpha) candidates of the same major/minor/patch version.

In Phase 2 with ephemeral environments, the process remains the same for Feature branches. For Pull Request (PR) builds, if the package.json/csproj is not updated, a validation error is thrown; if it is updated, the image/build is tagged with a release candidate (-RC) and the build ID. The main branch version takes precedence over all Feature (alpha & RC) candidates. With ephemeral environments, each feature deployment will deploy a unique pod (application & infrastructure).

## Phase 1 Strategy – versioning logic (before ephemeral environments)¶

**Feature branch build and deployments**

1. Retrieve the version from the Main branch package.json for the repository (e.g.: 4.2.30)
  1. if main and feature branches are the same version (M/M/P) then:
     1. throw validation error message: "The increment is invalid. Users must increase the package.json version.". Do not continue CI.
  2. if main and feature branch version are not same (i.e., a developer has increased Major, Minor or Patch) and Feature Branch > Main branch version, then:
     1. Tag the image and build with ‘-alpha’ and build ID which becomes: 4.2.31-alpha.511210 and respect the supplied major/minor/patch.
2.	Push this version to Container Registry (ACR) when a deploy is requested. 

**Pull Request (PR) builds and deployments**

No change for Phase 1, including tagging and naming. Developers merge (feature branch) version must be always above main.

 
**Main branch build and deployments**

1.	New version example is: 4.2.31 (patch+1).
2.	This version will be pushed to the ACR on deployment after merge into main.
3.	The main branch version is the primary version which takes precedence above all feature (alpha) candidates of the same major/minor/patch.

## Phase 2 versioning logic – (with ephemeral environments are in place)

**Feature branch builds and deployments**

1.	Retrieve the version from Main branch package.json/csproj for the repository (e.g. 4.2.30)
  1. if main and feature branch are the same version (M/M/P) then:
     1. throw validation error message: "The increment is invalid. Developers must increase the package.json version.". Do not continue CI.
  2. if main and feature branch version are not same (i.e., a developer has increased Major, Minor or Patch) and Feature Branch > Main branch then:
     1. Tag the image and build with ‘-alpha’ and ‘build ID’ which becomes 4.2.31-alpha.511210 and respect users major/minor/patch.
  3. Push this version to ACR when a deploy is requested. 

**Pull Request (PR) builds and deployments**

1.	If package.json/csproj is not updated in the repository then throw validation message: "The increment is invalid '4.2.30' -> '4.2.30'. Please upgrade". Do not continue CI.
2.	If package.json/csproj is updated (i.e., 4.2.31) then tag the image and build with the release candidate (-RC) and build ID which becomes: 4.2.31-rc.511211
3.	Push this version to the Container Registry (ACR) when a deploy is requested. 

**Main branch**

1.	New version example is:  4.2. 31 (patch+1).
2.	This version will be pushed to the Container Registry (ACR) on deployment after merge into main.
3.	The main branch version is the primary version which takes precedence over and above all feature (alpha & RC) candidates of the same major/minor/patch.

Constraints

- Feature deployments into Sandpit/Dev will overwrite the existing deployment in terms of app code, infrastructure, and databases in Phase 1. This can cause conflicts and constraints.
- Once ephemeral environments are delivered, PR and Feature deployments into Sandpit/Dev will have its own dedicated infrastructure, including Application, Infra and Databases.
- SemVer and Trunk based development are mandated and designed into the Platform.
- All merges into ‘main’ are classed as releases and are tagged in GitHub as such with the application version supplied. 
- Long-lived feature branches are not allowed and are discouraged. To deploy into a higher environment above Sandpit, you must merge into Main. 

# Platform service Deployment Strategy

## Guidance and Context

This article outlines the Platform deployment strategy. Development teams should read the Platform Versioning and Git strategy document before reading this. ADP’s primary deployment strategy is **Rolling Deployments** with [HELM](https://atlassian.github.io/data-center-helm-charts/userguide/upgrades/HELM_CHART_UPGRADE/#3-define-the-upgrade-method), [AKS](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/) and [FluxCD](https://fluxcd.io/flagger/usage/deployment-strategies/). This provides Platform services with a zero-downtime deployment strategy. This allows applications to achieve high availability with low/no business impact. This is important for services that need 24/7 availability and allows the capability to deploy to production multiple times a day. In the future, we will support other deployment strategies, including Blue-Green and Canary deployments for custom scenarios, including service ‘shutter pages’.


## Rolling Update strategy
ADP uses HELM with Kubernetes, and Flux to perform rolling deployments. The default strategy applied to all services is rolling deployments. There are 3 core parts to a Service deployment: Web Application, Service Infrastructure, and App Configuration including Secrets. 
The process flow is as follows:

1. A new deployment is triggered via the CI and CD Pipelines for the Service.
  1. New Secrets are imported/updated/deleted in the environment Key Vault and are mastered via the ADO Secret Library Groups for the environment and service
  2. New App Configuration keys/values/settings are imported/updated/deleted in the Config Maps & App Configuration Service from the service’s ‘appConfig.yaml’ in their repository. These will be ready for the new/updated app/pods to consume. The sentinel key is not updated.
2. The new images and artefacts are pushed to the environment Container Registry (ACR) (pipeline deployment) and Flux automatically updates the Services repository with the new version to be deployed.
   1. Note: This can be a higher version (new image) or lower version (existing/rollback).
3. Flux reconciles the Cluster with the new App and Infrastructure version requested with a rolling update. Any infrastructure updates take precedence over Application (Infra then App).

  **Application deployment:**
   
   1. The deployment will incrementally add the new Pods (applications) on the Nodes in the Cluster. This will automatically pick up the new App Config and Secret updates.
   2. AKS will wait for those new Pods to start successfully with the configured/default wait times and health check endpoints.
   3. Once the new pods are reporting healthy with a valid health status, traffic will then be directed to the new Pods (updated app) via the internal load balancer/NGINX gracefully.
   4. The old Pods will be deleted incrementally if the new pods and app has started successfully, and all traffic has drained.
   5. If the new App does not start successfully, the deployment will time out and fail after a set period (5 minutes), but the old application will remain in place and accepting traffic. The old configuration will remain intact for App Config only for the previously deployed version. Unhealthy Pods will be removed if an upgrade fails.
  
  **Infrastructure deployment:**

   1. The new infrastructure will be deployed and either created, updated, or deleted.
   2. Once updated successfully, the App and Database (If applicable) can be deployed or upgraded.
   
  **Database deployment:**   

   1. If a new Schema is to be deployed, this will be done before the Application is deployed as a dependency.
   2. If database deployment fails, the App will not be upgraded.

  
1. If a user has requested the deployment of App Config/Secrets only, the App or Infra will not be deployed.
   1. The App Config & Secrets will be updated via the Pipeline, including the Sentinel Key with the Build ID – which triggers the configuration update.
   2. The [Reloader](https://github.com/stakater/Reloader) service will perform a rolling and zero-downtime upgrade of the service with the new configuration (incrementally replace new pods with old).

## Deployment and App Configuration / guidance 

All services will have the following settings defined or defaulted:

- maxSurage – maximum additional Pods created at one time (50%).
- maxUnavailable – max Pods not available (25%)
- podDisruptionBudget – allowed disruptions for a Pod (application) (25% or at least 1)
- min and max replicas – number of replicas of the application in the Cluster. Minimum of 3 for production for high availability. 
- All deployments of business apps are on the Users/Apps Node Pools. Platform/System apps are on the System Node Pool.
- Autoscaling via HPA is enabled.

## Constraints

- Database updates, if using PostgreSQL, will require development teams to deploy non-breaking changes and/or manage their schema updates appropriately with their app deployment.
- Shutter pages will be included in phase 2 / Post MVP if required.
- Development teams must set health endpoints correctly for an effective rolling update.
- App Config, Infrastructure and Application Code are tied together as an immutable unit. They are versioned using semver strategy defined in the versioning document.
- App Secrets in Key Vault are not versioned with the App and Code, they are independent, and can be rotated periodically. 
  - All secret rotations must have an overlap in expiry periods to ensure zero-downtime upgrades.
- The Platform has defined minimum replicas, and minimum availability to meet Defra SLA’s.
- Reloader will drain and replace the Pods in the Cluster with a rolling upgrade on detection of new App Config or New App Secrets automatically.
- All HELM Deployments are full CRUD operations – add, update, or delete. This includes Apps, Infra and Databases.
- All App Configuration updates are full CRUD operations – add, update, or delete.
- Secrets are add/update only for MVP.