---
title: How to create an Accessibility test
summary: How to create and run Accessibility test for a service
uri: https://defra.github.io/adp-documentation/How-to-guides/how-to-create-accessibility-test/
authors:
  - Rajesh Venkatraman
date: 2024-08-14
weight: 2
---

## How to Accessibility test

In this how to guide you will learn how to create, deploy, and run an Accessibility test for a Platform service (Web App, User Interface etc) for your team.

## Prerequisites

Before adding tests for your service, you will need to ensure that:

- [Onboarded delivery project on to ADP](../../Getting-Started/onboarding-a-delivery-project.md)
- [Created a Platform Service for your team/delivery project](../../How-to-guides/Platform-Services/how-to-create-a-platform-service.md)

## Overview

By completing this guide, you will have completed these actions:

- [x] Learned how to add an Accessibility test for your service.
- [X] Learned how to run an Accessibility test locally.
- [X] How to customize your pipeline to run Accessibility tests for different env.

## Guide

!!! note
    Every pipeline run includes steps to run varoious tests pre deployment and post deployment. These tests may include unit, integration, acceptance, performance, accessibilty etc as long as they are defined for the service.

    The pipeline will check for the existence of the file `.docker-compose.axe.yaml` to determine if Accessibility tests have been defined.

### How to add an Accessibility test for your service?

Refer to the [ffc-demo-web example](https://github.com/DEFRA/ffc-demo-web/blob/main/docker-compose.axe.yaml).

#### Requirments for local development

- Docker Desktop 2.2.0.3 (42716) or higher

### How to run an Accessibility test locally?

Executre the above commands in bash or PowerShell

```ps1

# this will execute the docker-compose at the root folder to create an instance of the service and its dependences
docker-compose -f docker-compose.yaml -f docker-compose.axe.yaml run --rm axe

```

### How to customize your pipeline to run Accessibility tests?

You can customize the environments where you would like to run Accessibility test (within the pipeline it is referred as integration test)

```yaml
postDeployTest:
  envToTest: 'snd4, dev1'
  testEnvs:
    accessibilityTests: 'snd4, dev1'
  testsToRun: 'accessibility'
```

If not defined, the pipeline will run with following default settings.
```yaml
postDeployTest:
  envToTest: 'snd4, dev1, tst1, pre1'
  testEnvs:
    accessibilityTests: 'snd4'
  testsToRun: 'owasp;accessibility;performance;service-acceptance;acceptance'
```

[Please refer ffc-demo-web pipeline:](https://github.com/DEFRA/ffc-demo-web/blob/main/.azuredevops/build.yaml)

### How to disable test?

if you want to disable the test for any reason please refer [Disable Test](../../How-to-guides/Testing/how-to-disable-test.md)