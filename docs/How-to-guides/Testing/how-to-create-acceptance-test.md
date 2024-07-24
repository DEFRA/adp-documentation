---
title: How to create an acceptance test
summary: How to create and run acceptance test for a service
uri: https://defra.github.io/adp-documentation/How-to-guides/how-to-create-acceptance-test/
authors:
  - Rajesh Venkatraman
  - Ken Babigumira
date: 2024-07-17
weight: 2
---

## How to acceptance test

In this how to guide you will learn how to create, deploy, and run an acceptance test for a Platform service (Frontend Web App or an API) for your team.

## Prerequisites

Before adding acceptance tests for your service, you will need to ensure that:

- [Onboarded delivery project on to ADP](../../Getting-Started/onboarding-a-delivery-project.md)
- [Created a Platform Service for your team/delivery project](../../How-to-guides/Platform-Services/how-to-create-a-platform-service.md)

## Overview

By completing this guide, you will have completed these actions:

- [x] Learned how to add an acceptance test for your service.
- [X] Learned how to run an acceptance test locally.
- [X] How to customize your pipeline to run an acceptace tests based on tags for different env.

## Guide

These tests may include unit, integration, acceptance, performance, accessibilty etc as long as they are defined for the service.

> The pipeline will check for the existence of the file `test\acceptance\docker-compose.yaml` to determine if acceptance tests have been defined.

### How to add acceptance test for your service?

You may add tags to features and scenarios. There are no restrictions on the name of the tag. Recommended tags include the following: @sanity, @smoke, @regression
[refer](https://github.com/DEFRA/ffc-demo-web/blob/main/test/acceptance/features/subsidenceStartDate.feature)

If custom tags are defined, then the pipeline should be customized to run those tests as detailed in following sections.

### How to run acceptance test locally?

#### Set required tags, default is empty string which will run all tests

- pwsh : `$ENV:TEST_TAGS = "@sanity or @smoke"`
- shell: `export TEST_TAGS = "@sanity or @smoke"`

#### Run the acceptance test script under scripts folder within the repo

```shell
docker-compose up -d
cd test/acceptance
docker-compose run --rm wdio-cucumber
```

### How to customize your pipeline to run acceptace tests?

Every pipeline run includes steps to run various post deployment tests.
These tests may include unit, integration, acceptance, performance, accessibilty etc as long as they are defined for the service.

You can customize the tags and environments where you would like to run specific features or scenarios of acceptance test

```yaml
postDeployTest:      
  testEnvs:
    performanceTests: snd4, pre1
    accessibilityTests: snd4, tst1,     
    acceptanceTests:
      - env: snd4
        tags: '@demotag'
      - env: dev1
        tags: '@sanity or @smoke'
  envToTest: snd4,dev1,tst1,pre1
```

If not defined, the pipeline will run with following default settings.

```yaml
postDeployTest:      
  testEnvs:
    performanceTests: snd4, pre1
    accessibilityTests: snd4, dev1, tst1    
    acceptanceTests:
      - env: snd4
        tags: '@sanity or @smoke'
      - env: dev1
        tags: '@smoke'
      - env: tst1
        tags: '@smoke or @regression'         
  envToTest: snd4,dev1,tst1,pre1
```

[Please refer ffc-demo-web pipeline:](https://github.com/DEFRA/ffc-demo-web/blob/main/.azuredevops/build.yaml)

Test execution reports will be available via Azure DevOps Pipelines user interface for your project and service.
