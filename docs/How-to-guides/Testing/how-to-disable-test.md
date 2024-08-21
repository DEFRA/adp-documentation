---
title: How to disable a test
summary: How to disable test for a service
uri: https://defra.github.io/adp-documentation/How-to-guides/how-to-disable-test.md/
authors:
  - Rajesh Venkatraman
date: 2024-08-21
weight: 2
---

## How to disable test

In this how to guide you will learn how to disable test for the service

### How to disable one specific test?

if you want to disable a specific test for any reason, please remove the test from 'testsToRun' line.

```yaml
postDeployTest:
  testsToRun: 'owasp;accessibility;performance;service-acceptance;acceptance;contract;integration'
```

example performance test

```yaml
postDeployTest:
  testsToRun: 'owasp;accessibility;service-acceptance;acceptance;contract;integration'
```

### How to disable all tests?

if you want to disable all tests, please set 'testsToRun' to 'none'

```yaml
postDeployTest:
  testsToRun: 'none'
```

[Please refer ffc-demo-web pipeline:](https://github.com/DEFRA/ffc-demo-web/blob/main/.azuredevops/build.yaml)
