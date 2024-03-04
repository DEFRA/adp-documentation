---
title: Quality Assurance & Testing Approach
summary: Quality Assurance & Testing approach for both platform and its services.
authors:
    - Logan Talbot
    - Kenneth Babigumira
    - Dan Roszkowski
    - Clive Hackney
date: 2024-03-04
weight: 1
---
# Quality Assurance & Testing Approach

## Platform Quality Assurance & Testing Approach

## Service Quality Assurance & Testing Approach

## Test Tooling

| Test Type                   | NodeJs                    | .NET                      | Considerations | Local Tool | Pipeline Tool | License required |
| --------------------------- | ------------------------- | ------------------------- | -------------- | ---------- | ------------- | ---------------- |
| Unit Test                   | Junit, Jest               | Nunit, Xunit, NSubsistute |                | Y          | Y             | N/OS             |
| Functional                  | WebDriver.io, Cucumber    | Selenium, SpecFlow        |                | Y          | Y             | N/OS             |
| Browser                     | Browserstack              | Browserstack              | Licensing      | Y          | Y             | Y                |
| Performance                 | JMeter, Azure Load Tester | JMeter, Azure Load Tester |                | Y, N       | Y             | N/OS             |
| Security                    | OWASP ZAP                 | OWASP ZAP                 |                | Y          | Y             | N/OS             |
| Dependency/ Containers Scan | Snyk                      | Snyk                      | Licensing      | Y          | Y             | N                |
| Static Code Analysis        | SonarCloud                | SonarCloud                | Licensing      | N          | Y             | Y                |
| API Testing                 | Postman, Newman           | Postman, Newman           |                | Y          | N             | N/OS             |
| Contract Tests              | Pact Broker               | Pact Broker               |                | Y          | Y             | N/OS             |
| Accessibility               | Axe, Light house          | Axe, Light house          |                | Y          | Y             | Y                |

## When are tests ran?
