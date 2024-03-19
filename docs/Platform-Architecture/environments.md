---
title: Environments in ADP
summary: Overview of the environments in the Platform
authors:
    - xxx
date: 2024-03-14
weight: -11
---
!!! note "TODO"
    This page is a work in progress and will be updated in due course. Needs environments updated.

# Environments in ADP

The table below details the environments the Platform supports, there purposes, and whether they're mandatory for going live / on the RTL path.

* 1-1 mapping between Services/Tenants Environments & Azure Subscriptions
* The number at the end of the environment code designates the Subscription/Environment number for CCoE reference.
* A _Tenant/Customer/Service_ means a 'customer or team user the platform' and **not** the Platform team itself.
* Infrastructure-Dev is only for Platform Engineers.

## Core ADP Environments for Tenants & Infrastructure teams

| **Principal Environment Name** | **Use case**                                                                       | **Route-to-live** | **Azure Environment Code/Subscription** | **Additional Information**                          | **Azure Tenant** |
| ------------------------------------ | ---------------------------------------------------------------------------------------- | ----------------------- | --------------------------------------------- | --------------------------------------------------------- | ---------------------- |
| Tenant-Production                    | Live Services, Public & Private beta.                                                    | Yes                     | AZR-ADP-PRD1                                  |                                                           | Defra                  |
| Tenant-Pre-Production                | Automated Acceptance testing prior to production.                                        | Yes                     | AZR-ADP-PRE1                                  | VPN Required.                                             | Defra                  |
| Tenant-Demo                          | External demonstrations, PEN tests etc.                                                  | No                      | AZR-ADP-TST2                                  | VPN Required.                                             | Defra                  |
| Tenant-Test                          | General testing, performance testing, exploratory testing                                | No                      | AZR-ADP-TST1                                  | Intended for demo's to external and internal stakeholders | Defra                  |
| Tenant-Development                   | Development                                                                              | Yes                     | AZR-ADP-DEV1                                  | VPN Required.                                             | Defra                  |
| Tenant-Sandpit                       | Pre-merge automated tests, Pull Request checks etc.                                      | No                      | AZR-ADP-SND4                                  | VPN Required.                                             | Defra                  |
| Infrastructure-Dev                   | Testing infrastructure changes, proof of concepts, experimentations. Platform Team Only. | No/N/A                  | AZD-ADP-SND1, SND2, SND3                      | VPN Required.                                             | 0365_DefraDev          |

## Shared Services and Management Subscriptions

| **Principal Environment Name** | **Use case**                                                                      | **Route-to-live** | **Azure Environment Code/Subscription** | **Additional Information**                    | **Azure Tenant** |
| ------------------------------------ | --------------------------------------------------------------------------------------- | ----------------------- | --------------------------------------------- | --------------------------------------------------- | ---------------------- |
| Shared Services 3                    | Management and Shared Services - Test and all environments below. POC/development area. | No/N/A                  | AZD-ADP-SSV3                                  | DefraDev Shared Services/management                 | 0365_DefraDev          |
| Shared Services 5                    | Management and Shared Tests - Production and all environments below. Live services.     | No/N/A                  | AZR-ADP-SSV5                                  | Contains live ACR. Live shared services/management. | Defra                  |

**The Subscriptions that map to the environments [documentation can be found here.](https://dev.azure.com/defragovuk/DEFRA-FFC/_wiki/wikis/DEFRA-FFC.wiki/14424/Management-Groups-and-Subscriptions-Topology?anchor=subscriptions)**

## Route to live overview

For all Service teams, the defined minimum 'route to live' path is:

- Dev > Pre-Prod > Production (Live).
  -- _All other environments are optional: SND, TST, DMO_ etc.

This means that Service Teams must have passed automated checks/smoke tests in the Pre-prod environment and any initial merge and validation checks in Development only before going Live. All other environments are there on-demand for teams. We may have additional environments if the future if needed, such as a dedicated ITHC/PEN Test area (or Demo can be used), but again these would be optional.

=== note
    When deploying from a previous image/artefact already deployed to an environment, no CI/build is required. Environments are selectable.
