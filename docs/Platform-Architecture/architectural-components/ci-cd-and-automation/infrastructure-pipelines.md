---
title: Infrastructure Pipelines
summary: Infrastructure pipelines for CI/CD and automation
authors:
    - Dan Roszkowski
date: 2024-03-14
weight: 1
---

# Infrastructure Pipeline Enhancements

The Platform core pipeline will be able to deploy all components (resources) that is within the defined environment. The pipeline will be able to deploy resources based on the resource category and/or resource type. This article describes the structure for the deployment of the resources based on ADO Pipelines and associated YAML files.

## **Key Principles** 

Prior to this enhancements, when deploying or testing a resource, developers will comment out some parts of codes within the deploy-platform-core.yaml file in order to deploy only the relevant templates. The other approach is to deploy the entire resources which takes more time and slows down processes. This latter approach may also come at an extra cost. 

### Azure Resource Deployment 

Wih the enhancement, developers can deploy resources based on the below categories:
 - All
 - Network - All
      - Network - VNET
      - Network - NSGs
      - Network - Route Tables
 - Monitoring - All
      - Monitoring - Workspace
      - Monitoring - Insights
      - Monitoring - Azure Monitor Workspace
      - Monitoring - Grafana
 - Managed Cluster - All
      - Managed Cluster - Private DNS Zone
      - Managed Cluster - Cluster
 - Front Door - All
      - Front Door - Core
      - Front Door - WAF
 - Application - All
      - Application - App Configuration
      - Application - Apps Container Registry
      - Application - PostgreSql Server
      - Application - Redis Cache
      - Application - Storage Account
      - Application - Service Bus
      - Application - Key Vault

**Deployment process**
On ADO pipeline, the above category of resources can be selected in a dropdown menu. All resources can be deployed when the option 'All' is selected. This option will deploy all components of Network, Monitoring, Managed Cluster, Front Door and Application. 
However, if a developer or tester needs to deploy only network resources, the selection will be 'Network - All'. With this option, all the network resources (VNET, NSGS and Route Tables) will be deployed.

Similarly, if the deployment is for testing a specific resource within network category, for example, VNET, they will be able to do so by selecting 'Network -VNET' option from the dropdown list. This will only deploy VNET template as defined in the yaml file.

**Implementation approach**
The categorisaton for resources have been applied with the use of 'parameters' defined within the deploy-platform-core-yaml file. The default value is 'All'.

Furthermore, 'variables' have been used to define each of the values listed in the 'parameters' section. 

Conditional statements are subsequently applied to filter the resources in the 'groupedTemplates'.
