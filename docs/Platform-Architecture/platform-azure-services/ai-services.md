---
title: Azure AI Services
summary: Design for AI services in Azure that are used offered for the platform.
uri: https://defra.github.io/adp-documentation/Platform-Architecture/platform-azure-services/ai-services/
authors:
    - Logan Talbot
date: 2024-05-15
weight: 1
---

!!! note "TODO"
    This page is a work in progress and will be updated in due course.


# Azure AI Services

This article details the AI Services Architecture for the solution at a high level.

!!! warning

    Please ensure you fellow DEFRA's guidelines and policies when using AI services. This includes the use of data and the use of AI services in general in order to ensure your delivery project is using AI responsibly.

## Azure Open AI Services
Azure OpenAI Service provides REST API access to OpenAI's powerful language models including the GPT-4, GPT-4 Turbo with Vision, GPT-3.5-Turbo, and Embeddings model series. In addition, the new GPT-4 and GPT-3.5-Turbo model series have now reached general availability. These models can be easily adapted to your specific task including but not limited to content generation, summarization, image understanding, semantic search, and natural language to code translation.

### Deployed & Supported Models

For supported models within Azure Open AI, ADP is restricted to the models supported by Azure in the [UK South region](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models). The models that are supported are:

| Model | Deployment | Quota | Description |
|-------|------------|-------|-------------|
| gpt-4 | gpt-4 | 80k | Improvement on GPT-3.5 and can understand and generate natural language and code. |
| gpt-35-turbo | gpt-35-turbo | 350k | Improvement on GPT-3 and can understand and generate natural language and code. |
| text-embedding-ada-002 | text-embedding-ada-002 | 350k | Can convert text into numerical vector form to facilitate text similarity. |
! text-embedding-3-large | text-embedding-3-large | 350k | Can convert text into numerical vector form to facilitate text similarity. the This is the latest and most capable embedding model. Upgrading between embeddings models is not possible  |

!!! warning

    Delivery Projects need to be a ware of quote restrictions of the models per subscription/ region as these models are shared between Delivery Projects. ADP can request more quota if required but this will require the approval from Microsoft.

### Architecture

With in the ADP Platform, Azure Open AI Services are deployed with an Azure API Management (APIM) to provide a secure and scalable API endpoint for the AI services. The APIM is used to manage the API lifecycle, provide security, and monitor the API usage. The APIM is deployed in a Subnet (/29) and uses a private link to connect to the Azure OpenAI service. The APIM is deployed with a developer portal to provide a self-service API documentation for the AI services. Between the delivery projects service and the APIM, private link will be implemented and the APIM will use the services' [managed identity](https://learn.microsoft.com/en-us/azure/api-management/api-management-authenticate-authorize-azure-openai#authenticate-with-managed-identity) with the role of `Cognitive Services OpenAI User` assigned. This will allow the APIM to access the AI services on behalf of the delivery project's service privately and securely.

Any other Azure services that need to access the AI services will need to use the APIM endpoint and managed identity to access the AI services.

#### Iterative Deployment

In order to meet the time lines and requirements of the delivery projects, we will first only deploy the Azure Open Service and give the AKS cluster and Azure AI Search direct access to the service over a private endpoint with the role of `Cognitive Services OpenAI User`.

This will allow the delivery projects to start using the AI services and provide feedback on the service. Once the APIM is deployed, we will migrate the AI services to APIM to provide a secure and scalable API endpoint for the AI services. 

!!! note
    
    For local development, the delivery projects can use the Azure Open AI services directly in SND only and connected to the DEFRA VPN or DEFRA laptop. This will allow the delivery projects to test the AI services and provide feedback on the service.

### Developer Access

For local development, developers will be able to access SND only via the DEFRA VPN or DEFRA laptop with the assigned role of `Cognitive Services OpenAI User`. Giving the developers the ability to test Azure Open AI services locally via APIM and view model deployments of the deployment Azure Open AI service.

For Dev plus environments, developers will be able to access SND only via the DEFRA VPN or DEFRA laptop with the assigned role of `Cognitive Services OpenAI User`. This current allow them to:

- View the resource in Azure Portal
- View the resource endpoint under “Keys and Endpoint” but not the keys.
- View the resource and associated model deployments in Azure OpenAI Studio
- View what models are available for deployment in Azure OpenAI Studio
- Use the Chat, Completions, and DALL-E (preview) playground experiences with any models that have already been deployed to this Azure OpenAI resource.

!!! note

    Dev plus environments, APIM endpoints will not be exposed to local developer access and will only remind accessible to ADP Azure services or authenticated Delivery project's services only.

### Quota & Token Management

The Azure Open AI services are shared between delivery projects and have a quota limit per subscription/ region. The quota limit is shared between the delivery projects and can be increased if required. The quota limit is monitored by the ADP team and will be increased if required with the approval from Microsoft.

For the ADPs platforms current needs and requirements, we opting for the pay-as-you-go pricing model for the Azure Open AI services. This will allow the delivery projects to only pay for what they use and not have to worry about overage charges. This does mean that there is a Tokens-per-Minute limit per model for the Azure Open AI services and the delivery projects will need to be aware of this limit when using the AI services.

In order to manage this an ensure efficient use of the Azure Open AI, APIM will provide policy enforcement to manage the quota limit and present an better experience to the delivery projects when the quota limit is reached:

- [Retry policy](https://learn.microsoft.com/en-us/azure/api-management/retry-policy): When the quota limit is reached, the APIM will return a 429 status code to the delivery project's service. The delivery project's service will need to implement a retry policy to handle the 429 status code and wait for the quota to be reset.
- [Token limit policy](https://learn.microsoft.com/en-us/azure/api-management/azure-openai-token-limit-policy) - By relying on token usage metrics returned from the OpenAI endpoint, the policy can accurately monitor and enforce limits in real time. The policy also enables precalculation of prompt tokens by API Management, minimizing unnecessary requests to the OpenAI backend if the limit is already exceeded.

### Monitoring

Uses APIM to increase monitoring of the usage of OpenAI per service you can use an [OpenAI Emit Token Metric policy](https://learn.microsoft.com/en-us/azure/api-management/azure-openai-emit-token-metric-policy) to send metrics to Application Insights about consumption of large language model tokens through Azure OpenAI Service APIs. Token count metrics include: Total Tokens, Prompt Tokens, and Completion Tokens with additional dimensions such as the User ID and API ID. Using these metrics, ADP can monitor the usage per service which can be used by the project delivery teams and ADP to infer usage and potentially request more quota if required but also for billing purposes.

For monitoring Azure Open AI directly we enable the [Azure OpenAI Service Diagnostic Logs](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/monitoring) to monitor the usage of the AI services directly. This log data will be stored in to Azure Monitor (Log Analytics Workspace) where ADP and the delivery projects will be able to gain insights such as:

- Azure OpenAI Requests: Total number of calls made to the Azure OpenAI API over a period of time.
- Generated Completion Tokens: Number of generated tokens (output) from an Azure OpenAI model.
- Processed Inference Tokens: Number of inference tokens processed by an Azure OpenAI model. Calculated as prompt tokens (input) + generated tokens.

### Possible Future Enhancements

#### Semantic caching for Azure OpenAI APIs in APIM

[Enable semantic caching of responses to Azure OpenAI API requests](https://learn.microsoft.com/en-us/azure/api-management/azure-openai-enable-semantic-caching) to reduce bandwidth and processing requirements imposed on the backend APIs and lower latency perceived by API consumers. With semantic caching, you can return cached responses for identical prompts and also for prompts that are similar in meaning, even if the text isn't the same.

#### More Azure OpenAI Service Model Deployments

With our current restrictions on the models that can be deployed in the UK South region, we are limited to the models that are supported by Azure. In the future, we will look to expand the models that are supported in the UK South region and provide more models for the delivery projects to use. For example support for Whisper, DALL-E, and GPT-4o models.

#### Reserved Capacity for Azure OpenAI Service

Reserved capacity, or Provisioned Throughput Units (PTU), for AOAI. The newer offering is PTU-M (Managed), where the backend compute is abstracted away, pooling of resources. Beyond the default TPMs described above, this Azure OpenAI service feature, PTUs, defines the model processing capacity, using reserved resources, for processing prompts and generating completions.

PTUs are purchased as a monthly commitment with an auto-renewal option, which will reserve AOAI capacity within an Azure subscription, using a specific model, in a specific Azure region. TPM and PTU can be used together to provide scaling within a single region.

PTU minimums are VERY expense thus requiring ADP to be at a certain scale to justify the cost between its Delivery Projects.

### Key Resources

- [Azure Open AI Service](https://learn.microsoft.com/en-us/azure/ai-services/openai/)
- [GitHub: Microsoft - AzureOpenAI-with-APIM](https://github.com/microsoft/AzureOpenAI-with-APIM)
- [GitHub: aoai-apim](https://github.com/Azure/aoai-apim)
- [Medium: Azure OpenAI Best Practice](https://medium.com/@manoranjan.rajguru/azure-openai-best-practices-for-production-b733eca4bde5)
- [Managed Identity with APIM](https://learn.microsoft.com/en-us/azure/api-management/api-management-howto-use-managed-service-identity)
- [Role-based access control for Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/role-based-access-control)
- [MS Learn: Import Azure Open Open AI Service into APIM](https://learn.microsoft.com/en-us/azure/api-management/azure-openai-api-from-specification) 

### Outstanding Questions

- Local usage of Azure Open AI?

## Azure Ai Search

