---
title: How to create a platform service
summary: How to create a platform service on to ADP.
uri: https://defra.github.io/adp-documentation/How-to-guides/how-to-create-a-platform-service/
authors:
  - Logan Talbot
  - Lorenzo Squillante
date: 2024-02-16
weight: 1
---

# How to create a platform service

In this how to guide you will learn how to create a new service on to ADP within your delivery project. You will also learn what automated actions that will take place.

## Prerequisites

Before onboarding a delivery project ou will first need to ensure that:

- [Onboarded delivery project on to ADP](../Getting-Started/onboarding-a-delivery-project.md).
- Active user on the ADP Portal with admin permissions for the delivery project on the ADP portal.
- With your chosen ADO project you will need to ensure that the `ADP Platform Service Account` is the correct permissions for scaffolding. These permissions are, Project, Build, and Endpoint Administrator. Read & Manage Environments

## Overview

By completing this guide you will have completed these actions:

- [X] Creates a GitHub team for the services' delivery project if not already present in Defra GitHub org.
- [X] Scaffolds a application in your selected language and defines optional infrastructure.
- [ ] Scaffolding of ADO project if not already done.
  - Share service connection and agent pools with ADO project.
  - Create ADO environment and variable group in ADO project.
- [X] Creation of ADO pipeline for the CI/CD for service in selected ADO project/ location.
- [X] Authorizes service connection between new created ADO Pipeline & GitHub repository.
- [X] Starts the running of ADO pipeline. Build application and SND3 without approval.
  - Init Sonar Cloud, Snyk, etc.
- [X] Register in Backstage catalog (via catalog info file).

### Areas of support

These areas current requires the support of the ADP Platform Team

- [ ] Domain creation
- [ ] Flux manifasts

# Guide

## Selecting a template

1. On the ADP portal click create on the left hand side menu.
2. Select the choose button of the template of the service you would like to create.

## Entering Component Information

Enter properties describing your component/ service:

1. Enter component name (service name). It musted be a unique name of the component which will be used for the repository, package, and image names. This should be in the format `{programme}-{project}-{service`}. For example fcp-grants-web.
2. Enter Description. Describes what this component does. This description will be used in the component's README and package.json.
3. Select the system that this component will be a part of. Systems are a collection of related components and resources.
4. Select the project phase which suits this component. Refer to the [GDS service manual](https://www.gov.uk/service-manual/agile-delivery) for more information.
5. Select the team who will own this component.
6. Click the next button.

## Entering Git Repository information

To encourage coding in the open the repository will be public by default. Refer to the [GDS service manual](https://www.gov.uk/service-manual/technology/making-source-code-open-and-reusable) for more information.

The scaffolder will create a new repository and an associated team with write permissions:

1. The host where the repository will be created.
2. Select GitHub org of defra.
3. Enter name of the repository. Should be the same as component name (service name).
4. Enter GitHub Team Name. This team will be granted Write access to the repository.
5. Enter GitHub Team Description. An optional description of the team.
6. Enter GitHub Team Members. Using comma-separated list of GitHub usernames to be added to the team. For example `GitHubName1,GitHubName2`.
7. Select GitHub Team Visibility. This is privacy level this team should have. By selecting *Visible* teams can be seen by all members in the organization. *Secret* teams can only be seen by the organization owners and team members.
8. Click next to move to the next page.

## Entering CI/CD information

CI/CD pipelines will be created in Azure DevOps:

1. Enter Azure DevOps Organization. This should be defaulted to `defragovuk`.
2. Enter Azure DevOps Project.
3. Enter Service Connection Name. This will be the name of the Service Connection used to connect to GitHub.
4. Enter Pipeline Folder. Folder path where the pipeline will be created in your project's ADO piplines. For example `adp/fcp-grants-web`.
5. Click review to move to the next page.

## Reviewing entered information

1. Review the information entered and click back if you would like to amend any of the provided information.
2. If you think all entered information is correct click the **create** button to being creation of your new service.

## Creating the service

Now that you have reviewed and confirmed the creation of your service. It will be complete the actions detailed in the overview section. After this is complete links will be provided to the new repository, the service link in the ADP portal (Catalog), and to the Azure CI Pipeline which is current running.

![creation of service in portal](../images/creation-of-service.png "Creation of Service in Portal")

# Creation of additional infrastructure
