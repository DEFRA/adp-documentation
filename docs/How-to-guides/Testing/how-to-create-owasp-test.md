---
title: How to create an OWASP test
summary: How to create and run OWASP test for a service
uri: https://defra.github.io/adp-documentation/How-to-guides/how-to-create-owasp-test/
authors:
  - Rajesh Venkatraman
date: 2024-08-14
weight: 2
---

## How to OWASP test

In this how to guide you will learn how to create, deploy, and run a OWASP test for a Platform service (Web App, User Interface etc) for your team.

## Prerequisites

Before adding tests for your service, you will need to ensure that:

- [Onboarded delivery project on to ADP](../../Getting-Started/onboarding-a-delivery-project.md)
- [Created a Platform Service for your team/delivery project](../../How-to-guides/Platform-Services/how-to-create-a-platform-service.md)

## Overview

By completing this guide, you will have completed these actions:

- [x] Learned how to add a OWASP test for your service.
- [X] Learned how to run a OWASP test locally.
- [X] How to customize your pipeline to run OWASP tests for different env.

## Guide

!!! note
    Every pipeline run includes steps to run varoious tests pre deployment and post deployment. These tests may include unit, integration, acceptance, performance, accessibilty etc as long as they are defined for the service.

    The pipeline will check for the existence of the file `.docker-compose.zap.yaml` to determine if contract tests have been defined.

### How to add a OWASP test for your service?

The OWASP Test Config should be added to the `zap` folder in the GitHub repository of the service. Refer to the [ffc-demo-web example](https://github.com/DEFRA/ffc-demo-web/tree/main/zap). This folder should contain `zap.conf` file which is required to execute the tests.

#### Requirments for local development

- Docker Desktop 2.2.0.3 (42716) or higher

### How to run a OWASP test locally?

Executre the above commands in bash or PowerShell

```ps1

# this will execute the docker-compose at the root folder to create an instance of the service and its dependences
docker-compose -f docker-compose.yaml -f docker-compose.zap.yaml run --rm zap-baseline-scan

```

### How to customize your pipeline to run OWASP tests?

You can customize the environments where you would like to run OWASP test (within the pipeline it is referred as integration test)

```yaml
postDeployTest:
  envToTest: 'snd4, dev1'
  testEnvs:
    owaspTests: 'snd4, dev1'
  testsToRun: 'owasp'
```

If not defined, the pipeline will run with following default settings.
```yaml
postDeployTest:
  envToTest: 'snd4, dev1, tst1, pre1'
  testEnvs:
    owaspTests: 'snd4, dev1, tst1, pre1'
  testsToRun: 'owasp;accessibility;performance;service-acceptance;acceptance;contract;integration'
```

[Please refer ffc-demo-web pipeline:](https://github.com/DEFRA/ffc-demo-web/blob/main/.azuredevops/build.yaml)

### How to disable test?

if you want to disable the test for any reason please refer [Disable Test](../../How-to-guides/Testing/how-to-disable-test.md)