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


!!! warning

    Delivery Projects need to be a ware of quote restrictions of the models per subscription/ region as these models are shared between Delivery Projects. ADP can request more quota if required but this will require the approval from Microsoft.

### Deployment of Azure Open AI Services

TBC

### Key Resources

- [Azure Open AI Service](https://learn.microsoft.com/en-us/azure/ai-services/openai/)
- [GitHub: Microsoft - AzureOpenAI-with-APIM](https://github.com/microsoft/AzureOpenAI-with-APIM)
- [GitHub: aoai-apim](https://github.com/Azure/aoai-apim)
- [Medium: Azure OpenAI Best Practice](https://medium.com/@manoranjan.rajguru/azure-openai-best-practices-for-production-b733eca4bde5)

## Azure Ai Search

