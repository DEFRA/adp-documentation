---
title: Scaling of the Platform
summary: How we scale the platform
authors:
    - xxx
date: 2024-03-14
weight: 1
---
Scaling of the Platform

!!! note "TODO"
    This page is a work in progress and will be updated in due course.

On the Platform we're acutely aware we'll be hosting a large variety of services, with a wide range of requirements. We need to understand and define how we'll manage and scale, based on legitimate requirements and workload demands. Here, we'll cover this in terms of tenant applications, azure infrastructure, storage capacity, service limits, pipelines, ADO projects etc.

## **What do you scale?**

The platform. But specifically:

- Azure Kubernetes Service (AKS) - Cluster, Nodes, & Apps/Pods
- Azure Storage Locations (Log Analytics, Storage accounts, etc.)
- PostgreSQL Flexible Server - CPU, Memory & Storage
- Azure Cosmos DB
- Azure Front Door
- App Gateways and Ingress Controllers
- Deployment Pipelines
- Azure PaaS (Keyvault, App Config, etc.)
- Azure Redis
- Azure Service Bus
- Azure API Management

## **How do you scale?**

We'll use a range of tools and features based on the component which we'll detail below. We'll cover both our infrastructure and service scaling configuration, as well as how we scale Platform Teams, Tenants and Services appropriately (i.e. supporting tooling, pipelines, projects, repos, etc.).

## **Scaling Configurations**

### Azure Kubernetes Services

### PostgreSQL Flexible Server
