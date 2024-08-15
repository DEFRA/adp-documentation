---
title: Flux Manifests Generation Process
summary: Describes the process for generating the Flux manifests
authors:
    - Ken Babigumira
date: 2024-07-15
weight: 1
---


# Flux Manifests

Flux manifests are YAML files that define the desired state of your Kubernetes resources. They are essential for managing and automating the deployment of applications and infrastructure in a Kubernetes cluster.

## Why We Need Flux Manifests

- **Declarative Configuration**: Flux manifests enable you to declare the desired state of your resources, making it easier to manage and maintain configurations.
- **Version Control**: By storing manifests in a Git repository, you can track changes, roll back to previous states, and collaborate with others.
- **Automation**: Flux continuously monitors the Git repository and applies changes to the cluster, ensuring that the cluster state matches the desired state defined in the manifests.

## Types of Flux Manifests

1. Kustomization: Defines how to customize Kubernetes resources using Kustomize.
1. HelmRelease: Manages Helm charts and releases.
1. GitRepository: Specifies the source Git repository containing the manifests.
1. HelmRepository: Specifies the source Helm repository containing the manifests.
1. ImagePolicy: Specifies policies for automated image updates.
1. ImageRepository: Defines the source container image repository.

## Microsoft Flux Extension for AKS

The Microsoft Flux extension for Azure Kubernetes Service (AKS) integrates GitOps capabilities directly into AKS clusters. So rather than managing the set up of Flux, this extension is enabled and configured on the cluster. It simplifies the management of Kubernetes clusters by leveraging GitOps principles, making it easier to maintain, scale, and secure your deployments

### Flux Operator

The Flux Operator is a Kubernetes controller that manages the lifecycle of Flux CD. [It automates the installation, configuration, and upgrade of Flux controllers based on a declarative API](https://github.com/controlplaneio-fluxcd/flux-operator)

## Generating the Flux Manifests

![image.png](../../../../images/core-platform/flux/Flux-Manifests-Generation-Process.png)

1. A Delivery Project is created following the process described in [the Onboarding a delivery project section](../../../../Getting-Started/onboarding-a-delivery-project.md).  The portal will make an API POST request to the backend API to create the Team specific AD Groups to be used for RBAC.
2. The Backend API will populate the details of the teams in the Teams config repository.
   On creation of the project, the teams config will be updated (team specific AD groups).
3. The Backend API will then sync the group changes and create the AD Groups in Microsoft Entra ID. This occurs on creation and update on the delivery project information in the ADP Portal.
4. Once a Delivery Project has been created, the development teams can then scafford one or more services following the documentation in the guide [How to create a service guide](../../../../How-to-guides/Platform-Services/how-to-create-a-platform-service.md)
5. When a user submits the service template form that the engineer has completed, the portal will make API requests to the backend service.
6. The Backend service will start by writing the information to the Teams config.
7. The API will then create/update the Flux manifests for the Sandpit environments in the Flux Services repository. For upstream environments (dev, tst, pre and prd), the CI pipeline will add/update the flux manifests on demand during the deployment of the services to that environment. 
8. Flux will reconcile and deploy the application.