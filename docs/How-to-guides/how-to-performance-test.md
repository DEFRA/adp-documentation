---
title: How to performance test
summary: How to create and run performance test for a service
uri: https://defra.github.io/adp-documentation/How-to-guides/how-to-performance-test/
authors:
  - Rajesh Venkatraman
date: 2024-07-17
weight: 2
---

## How to run performance test

In this how to guide you will learn how to create, deploy, and run performance test for a Platform service (Web App, User Interface etc) for your team.

## Prerequisites

Before adding acceptance tests for your service, you will need to ensure that:

- [Onboarded delivery project on to ADP](../Getting-Started/onboarding-a-delivery-project.md)
- [Created a Platform Service for your team/delivery project](../How-to-guides/how-to-create-a-platform-service.md)

## Overview

By completing this guide, you will have completed these actions:

- [x] Learned how to add performance test for your service.
- [X] Learned how to run performance test locally.
- [X] How to customize your pipeline to run performance tests for different env.

## Guide

### How to add performance test for your service?

Peformance tests for the service can be added similar tothe existing demo service [ffc-demo-web](https://github.com/DEFRA/ffc-demo-web/tree/main/test/performance).

The framework is based upon [jmeter](https://jmeter.apache.org/), and utilises an jmeter image from [docker-jmeter](https://github.com/justb4/docker-jmeter).

### How to run performance test locally?

#### Run the performance test script under scripts folder within the repo

Please refer script [ffc-demo-web](https://github.com/DEFRA/ffc-demo-web/blob/main/scripts/jmeter)

### How to customize your pipeline to run performance tests?

Every pipeline run includes steps to run various tests, both pre deployment and post deployment.
These tests may include unit, integration, acceptance, performance, accessibilty etc as long as they are defined for the service.
Build pipeline will run the performance test if the following file exists
`test/performance/docker-compose.jmeter.yaml`

You can customize the environments where you would like to run specific features or scenarios of performance test

```yaml
postDeployTest:      
  testEnvs:
    performanceTests: pre1
  envToTest: snd4,dev1,tst1,pre1
```

if not defined, the pipeline will run with following default settings

```yaml
postDeployTest:      
  testEnvs:
    performanceTests: snd4, pre1
  envToTest: snd4,dev1,tst1,pre1
```

Please refer ffc-demo-web pipeline: [Refer](https://github.com/DEFRA/ffc-demo-web/blob/main/.azuredevops/build.yaml)

Test execution reports will be available via Azure DevOps Pipelines user interface for your project and service.
