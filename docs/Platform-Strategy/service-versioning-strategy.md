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
- The [Trunk Based Development Git](https://trunkbaseddevelopment.com/) git strategy for application development (code and infrastructure).

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

1.	New version example is: 4.2.31 (patch+1). Tag release in GitHub.
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

**Pull Request (PR) - builds and deployments**

1.	If package.json/csproj is not updated in the repository then throw validation message: "The increment is invalid '4.2.30' -> '4.2.30'. Please upgrade". Do not continue CI.
2.	If package.json/csproj is updated (i.e., 4.2.31) then tag the image and build with the release candidate (-RC) and build ID which becomes: **4.2.31-rc.511211**
3.	Push this version to the Container Registry (ACR) when a deploy is requested. 

**Main branch – build and deployments**

1.	New version example is:  4.2. 31 (patch+1). Tag release in GitHub.
2.	This version will be pushed to the Container Registry (ACR) on deployment after merge into main.
3.	The main branch version is the primary version which takes precedence over and above all feature (alpha & RC) candidates of the same major/minor/patch.

## Guidance / Context

- The Build ID is unique and is the ADO Pipeline build ID. It automatically increases on every CI on every image you request to be deployed (feature deployment).
- Developers must increment Major, Minor or Patch at least once, on a Feature branch build or PR build, to merge into main successfully. The build ID is automatically increased for subsequent deployments of the same version.
- The Main version takes priority over Alpha and RC candidates of the same major/minor/patch version.


## Constraints

- Feature deployments into Sandpit/Dev will overwrite the existing deployment in terms of app code, infrastructure, and databases in Phase 1. This can cause conflicts and constraints.
- Once ephemeral environments are delivered, PR and Feature deployments into Sandpit/Dev will have its own dedicated infrastructure, including Application, Infra and Databases.
- SemVer and Trunk based development are mandated and designed into the Platform.
- All merges into ‘main’ are classed as releases and are tagged in GitHub as such with the application version supplied. 
- Long-lived feature branches are not allowed and are discouraged. To deploy into a higher environment above Sandpit, you must merge into Main. 

# Platform service Deployment Strategy

## Guidance and Context

This article outlines the Platform deployment strategy. Development teams should read the Platform Versioning and Git strategy document before reading this. ADP’s primary deployment strategy is **Rolling Deployments** with [HELM](https://atlassian.github.io/data-center-helm-charts/userguide/upgrades/HELM_CHART_UPGRADE/#3-define-the-upgrade-method), [AKS](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/) and [FluxCD](https://fluxcd.io/flagger/usage/deployment-strategies/). This provides Platform services with a zero-downtime deployment strategy. This allows applications to achieve high availability with low/no business impact. This is important for services that need 24/7 availability and allows the capability to deploy to production multiple times a day. In the future, we will support other deployment strategies, including Blue-Green and Canary deployments for custom scenarios, including service ‘shutter pages’.