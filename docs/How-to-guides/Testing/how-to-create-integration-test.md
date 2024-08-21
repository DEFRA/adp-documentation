---
title: How to create an Integration test
summary: How to create and run an Integration test for a service
uri: https://defra.github.io/adp-documentation/How-to-guides/how-to-create-integration-test/
authors:
  - Rajesh Venkatraman
date: 2024-08-13
weight: 2
---

## How to Integration test

In this how to guide you will learn how to create, deploy, and run an Integration test for a Platform service (Web App, User Interface etc) for your team.

## Prerequisites

Before adding tests for your service, you will need to ensure that:

- [Onboarded delivery project on to ADP](../../Getting-Started/onboarding-a-delivery-project.md)
- [Created a Platform Service for your team/delivery project](../../How-to-guides/Platform-Services/how-to-create-a-platform-service.md)

## Overview

By completing this guide, you will have completed these actions:

- [x] Learned how to add an Integration test for your service.
- [X] Learned how to run an Integration test locally.
- [X] How to customize your pipeline to run Integration tests for different env.

## Guide

!!! note
    Every pipeline run includes steps to run varoious tests pre deployment and post deployment. These tests may include unit, integration, acceptance, performance, accessibilty etc as long as they are defined for the service.

    The pipeline will check for the existence of the file `./docker-compose.test.yaml` to determine if integration tests have been defined.

### How to add an Integration test for your service?

The Integration Test scripts should be added to the `test\integration` folder in the GitHub repository of the service. Refer to the [ffc-demo-web example](https://github.com/DEFRA/ffc-demo-web/tree/main/test/integration). This folder should contain `*.test.js` files which are required to execute the tests. Tests will be run using PACT BROKER service.

#### Requirments for local development

- Docker Desktop 2.2.0.3 (42716) or higher

### How to run an Integration test locally?

Executre the above commands in bash or PowerShell

```ps1


# this will execute the docker-compose at the root folder to create an instance of the service and its dependences
docker-compose -f docker-compose.yaml -f docker-compose.test.yaml -p "<<servicename>>-test" up

```

### How to customize your pipeline to run Integration tests?

You can customize the environments where you would like to run Integration test (within the pipeline it is referred as integration test)

```yaml
postDeployTest:
  envToTest: snd4
  testEnvs:
    integrationTests: snd4
  testsToRun: 'integration'
```

[Please refer ffc-demo-web pipeline:](https://github.com/DEFRA/ffc-demo-web/blob/main/.azuredevops/build.yaml)

### How to disable test?

if you want to disable the test for any reason please refer [Disable Test](../../How-to-guides/Testing/how-to-disable-test.md)