---
title: Dynamics 365 & Power Platform Integration Patterns
summary: Overview of Integration Patterns for Dynamics 365 and Power Platform
authors:
    - xxx
date: 2024-03-14
weight: -11
---
## Dynamics 365 & Power Platform Integration Patterns

A key feature for tenant teams is the integration with Microsoft Dynamics 365 and Power Platform. When integration with these components, Service Principals are used to generate Bearer tokens to authenticate to the instance. This document describes the best practices, networking routes and pros and cons of different solutions.

### **Dynamics 365**

#### **Azure Tenancy Alignment**

The Platform broadly aligns with the Defra Azure tenancies that the Microsoft SaaS products are hosted in. Namely, Defra and DefraDev. This reduces/removes the cross-tenant identity requirements and enables the use of Azure-managed identities with Microsoft Platform-managed secrets. A key requirement was to align identity solutions and ensure optimal automation.

#### **Authentication to Dynamics**

To connect to Azure services, the best practice approach is to use [Azure Managed Identities.](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview)  This can be used to connect to almost all Azure PaaS Services, as well as SaaS products such as Dynamics 365. This uses the industry standard OAuth 2 protocols - https://auth0.com/docs/get-started/authentication-and-authorization-flow/client-credentials-flow

- ADP Recommend and promote only using Azure Managed Identities to connect to Dynamics 365 with **OAuth 2.**

**Why do we recommend this?**

**Automation and security**

- Managed Identities are strongly preferred/required over service principals (app regs) and directly solve a key issue in Defra around Secret and Credential management and renewals. No Client Secret is required and no separate components are deployed.
- If not using an Azure MI, App Registrations must be created in Entra ID, adding to the automation and integration logic required to support, run and maintain. This would still be using OAuth2.
- App Reg Secrets expire every 1 year - and require an automated (or manual at worst) process to ensure these are kept up to date. An automated background process must be in place for App Registrations. With an MI, this process is managed by Microsoft securely.
- Managed identities directly solve this issue entirely within Azure as the Microsoft Platform itself automatically manages and renews the credentials without any user or service impact. The process is transparent and happens 6-monthly. No credentials are required to be stored anywhere.
- You don't need to store additional secrets and credentials, enhancing security presence.

**ADP Processes**

- Within ADP, a Workload Identity (Azure MI) is automatically created for each service out of the box on deployment. This same Identity can be used within Dynamics as an '**application user'** to integrate with 0365. No additional deployment is required in Azure.
- This removes the requirement for additional components (App Reg's) to be created and the same identity can be used for other scenarios. A dedicated MI can also be created, when required.
- No credential management or storage is required with the ADP recommended approach. Development teams can use NodeJS or C# SDKs to develop their applications and use this functionality easily with support from Microsoft / community.
- Note: The application user (the MI/WI) must be added to Dynamics with the relevant security profile and permissions. Reference articles below detail this fully, including any relevant SDKs.

Example reference articles: https://blog.yannickreekmans.be/secretless-applications-use-azure-identity-sdk-to-access-data-with-a-managed-identity/ and https://www.eugenevanstaden.com/blog/d365-managed-identity/

Using an MI to connect to Dataverse https://community.dynamics.com/blogs/post/?postid=09f639ba-5134-4bd1-8812-04e019b7b920

A deeper understanding of App Regs, OAuth and connetivity can be found here - reference articles: https://learn.microsoft.com/en-us/power-apps/developer/data-platform/authenticate-oauth#app-registration

https://www.vodovnik.com/2023/01/12/interacting-with-dataverse-data-from-azure-c/

#### **Networking**

When integrating with SaaS products, there are networking considerations to consider in terms of network security and ingress/egress charges. When working with Azure PaaS and SaaS Services, a number of options may be available to you depending on the pattern.

**Virtual network integration**  ** DRAFT **

Microsoft are introducing a number of enhancements to secure your applications running in Azure with SaaS products. One example is https://learn.microsoft.com/en-us/data-integration/vnet/data-gateway-power-platform-dataflows

These tools allows you to securely connect Azure Services to products like Power Platform and Dynamics securely - all within your own VNet without any public internet exposure, at lower cost.

ADP is building a future pattern around these scenarios and will be fully detailed here shortly. As the ADP Cluster is within an Azure VNET, VNET Integration is required for secure/none public connectivity. Alternatively, whitelisting via the ADP Front Door can be used to secure integrations. A pattern is being developed for this approach.

### **Power Platform**

### **Testing and Quality Assurance** ** DRAFT **

TBC -
