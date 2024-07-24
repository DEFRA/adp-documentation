---
title: ADP Quality Assurance
summary: This document provides an overview of the Quality Assurance Approach in the ADP.
uri: https://defra.github.io/adp-documentation/Developer-Reference/Quality-Assurance/Quality-Assurance-Overview/
authors:
    - Ken Babigumira
date: 2024-07-23
---

# ADP Quality Assurance Approach

This document outlines the QA approach for the Azure Developer Platform (APD). The objective of the quality assurance is to ensure that all business applications developed and hosted on the ADP meet DEFRA's standards of quality, reliability and performance.

The Quality Assurance approach follows the traditional QA Pyramid that modes how software testing is categorised and layered.


![ADP Testing Pyramid](../../images/qa/ADP%20QA%20Testing%20Pyramid.png)

## Guidelines

- All Testing Tooling that is used has to have been approved by the DEFRA Tools Authority
- Test Results must confirm to the agreed DEFRA standards e.g. 90% code coverage for unit tests.

## Selected Tools

Below are the tools that are currently supported on the ADP

|Type of Test|Tooling|
|---|---|
|Unit Testing|"C# NUnit/ xUnit, Nsubstitute: <br/> NodeJS: Jest |
|Functional/Acceptance|WebDriver.IO| Cucumber|
|Security Testing|"OWASP ZAP (Zed Attack Proxy) | Sonar Cloud|
|API Testing (Contract Testing)|PACT Broker|
|Accessibility Testing|AXE <br/> Lighthouse?|
|Performance Testing|JMeter, BrowserStack. <br/>Azure Load Testing is under consideration|
|Exploratory Testing (Manual)|ADO Test Plans|

## How to create Tests in the ADP

Development teams use the ADP Portal to scaffold a new service using one of the exemplar software templates (refer to [How to create a platform service](../../How-to-guides/Platform-Services/how-to-create-a-platform-service.md)). Based on the template type (frontend or backend), basic tests will be included that the teams can build on as they add more functionality to the service.

The ADP Platform provides the ability to execute the above tests. These tests are executed as post deployment tests. The pipeline will check for the existence of specific docker-compose test files to determine if it can run tests. Refer to the how-to-guides for the different types of tests.

> However, it is the responsibility of the delivery projects to ensure that the business services they are delivering have written sufficient tests for the different types of tests that meet DEFRA's standards.

### Unit Tests

The supported programming frameworks are .NET/C# and NodeJS/Javascript.

The unit tests are executed in the CI Build Pipeline. SonarQube analysis has been integrated in the ADP Core Pipeline Teplate to ensure the code conforms to the DEFRA quality standards.

Links to the SonarCloud analysis, Synk Analysis will available in the component page of the service in the ADP Portal.

### Functional/Acceptance Testing

These end-to-end tests for internal (via Open VPN) or public endpoints for frontends and APIs.

Refer to the [Guide on how to create an Acceptance Test](../../How-to-guides/Testing/how-to-create-acceptance-test.md)

### Performance Testing

These tests should be executed against internal (via Open VPN) or public endpoints for frontends and APIs. Docker is used with BrowserStack to execute the peformance tests.

As a pre-requisite, Non Functional Requirements should be defined by the delivery project to set the baseline for the expected behavior e.g. expected average API response time, page load duration.

There are various types of performance tests.

- Load tests access the peformance of the service under a typical and peak load
- Stress Load tests are intended to test the limits of the service.
- Spike tests are similiar to stress load tests, however, they test the service with sudden surges in traffic.
- Soak tests verify the reliability of the system over a long period time.

Refer to the [Guide on how to create a Performance Test](../../How-to-guides/Testing/how-to-create-performance-test.md)

### Accessibility Testing

These tests verify that the all DEFRA public websites/business services are in compliance with [WCAG 2.2 AA accessibility standard](https://www.gov.uk/service-manual/helping-people-to-use-your-service/understanding-wcag)

Refer to the guidiance on [Understanding accessibility requirements for public sector bodies](https://www.gov.uk/guidance/accessibility-requirements-for-public-sector-websites-and-apps)

### Security Testing

SonarQube Security Testing has been incorporated into the CI Build Pipeline. In addition to that, OWASP ZAP is executed as per of the post deployment tests.

