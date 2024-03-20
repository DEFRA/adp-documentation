---
title: Catalog Data Sources
summary: Catalog Data Sources for the ADP Portal
uri: https://defra.github.io/adp-documentation/Developer-Reference/adp-portal/ongoing-development//
authors:
    - James Coxhead
date: 2024-01-22
weight: -11
---
# Catalog Data Sources

## Overview

Catalog data is pulled in from multiple sources which are configured in the `app-config.yaml` file. Individual entities are defined by a YAML file.

## Catalog Sources

### Components

Backstage regularly scans the [DEFRA GitHub organisation](https://github.com/defra) for repos containing a `catalog-info.yaml` file in the root of the master branch. The FFC demo services contain examples of this file (see [ffc-demo-web](https://github.com/DEFRA/ffc-demo-web/blob/master/catalog-info.yaml)). New components scaffolded through Backstage will be contain this file (but it may need further customisation), existing components will need to have the file added in manually.

A `catalog-info.yaml` for a component file might look like this:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: "ffc-demo-web"
  description: |
    Digital service mock to claim public money in the event property subsides into mine shaft.
    This is the web front end for the application. It contains a simple claim submission journey where user input data is cached in Redis.
    On submission the data is pulled from Redis and passed to the message service.
  annotations:
    github.com/project-slug: DEFRA/ffc-demo-web
    dev.azure.com/project: DEFRA-FFC
    dev.azure.com/build-definition: DEFRA.ffc-demo-web
    sonarqube.org/project-key: adp-ffc-demo-web
  tags:
    - node
    - service-bus
    - redis
    - external
    - front-end
spec:
  type: frontend
  lifecycle: beta
  owner: "group:default/fcp-demo"
  system: fcp-demo-service
  dependsOn:
    - "resource:default/fcp-demo-claim-queue"
    - "resource:default/ADPINFSB01"
```

The [Backstage documentation](https://backstage.io/docs/features/software-catalog/descriptor-format#kind-component) describes the format of this file - it is similar to a Kubernetes object config file. The key properties we need to set are:

* **metadata\.name** - The name of the component. Must be unique, and should match the repository name.
* **metadata\.annotations** - Annotations are used by integrations with 3rd party systems. In the example above, `github.com/project-slug` is used to pull data from the specified project into the Pull Requests dashboard; the `dev.azure.com` annotations pull pipeline runs into the CI/CD dashboard; `sonarqube.org/project-key` pulls in Sonarcloud metrics for the specified project.
* **spec.type** - The type of component. In ADP we currently have two types - `frontend` (for a web application) and `backend` (for an API or backend service).
* **spec.lifecycle** - The state of the component. In ADP we have aligned the lifecycle with GDS project phases - discovery, alpha, beta, live, retirement.
* **spec.owner** - the group/team that owns the component. Groups are defined under shared entities below.
* **spec.system** - a reference to the system that the component belongs to. Systems are defined under shared entities below.
* **spec.dependsOn** - dependencies on other components and resources, e.g. if a service publishes to a message queue then a reference to that queue would be defined here.

If a component consumes infrastructure such as a database or service bus queue then that must also be defined alongside the component. Multiple entities can be defined in a single file by using a triple dash `---` to separate them.

### Shared entities

### Users

## Related Links

* [Backstage System Model](https://backstage.io/docs/features/software-catalog/system-model)
* [Backstage YAML format](https://backstage.io/docs/features/software-catalog/descriptor-format)
