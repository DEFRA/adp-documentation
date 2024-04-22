---
title: ADP Permissions Model
summary: Overview of the permissions model in the Platform
uri: https://defra.github.io/adp-documentation/Platform-Architecture/permissions-model/
authors:
    - Logan Talbot
date: 2024-03-14
weight: -11
---

# ADP Permissions Model

Overview of the roles and permissions within the ADP (Azure Development Platform). It outlines the different roles such as Platform User, Technical Team Member, Delivery Team Admin, Delivery Programme Admin, and ADP Admin, along with their respective descriptions and responsibilities. Explains the permissions associated with each role in the ADP Portal, Azure DevOps, and GitHub. It describes how permissions are stored in a database and Azure AD using AAD groups. Users are assigned to specific groups based on their roles, granting them the necessary permissions in the ADP Portal, GitHub, Azure, and Azure DevOps.

## ADP Roles

The table below details the roles in the Platform and their purpose they have:


| Scope | Role | Description |
|-------|------|-------------|
| Platform | Platform User | A user of the ADP Platform, who has access to the ADP Portal and can be a member of a Delivery Project or Programme. To do this, they must have a Cloud or DefraGovUK Account. |
| Delivery Project | Technical Team Member | Tech Lead, Tester, Developer, or Architect on the Delivery Team Member. |
| Delivery Project | Delivery Team Member | Member of the delivery project. |
| Delivery Project | Delivery Team Admin | Is a or Tech lead and/or Delivery Manager for the Project. |
| Delivery Programme | Delivery Programme Admin | Is a or Tech lead and/or Delivery Manager for the Programme. |
| Platform | ADP Admin | ADP Platform Engineering delivery team member. |
| Organization | CCoE Engineer | Central Cloud of Excellence Engineering engineer. |

!!! info
    Users with multiple roles will have the permissions of the highest role they have.

## Portal Permissions

The permissions for the portal are stored both in a database and in Azure AD with the use of AAD groups. The groups are named as follows:

- Delivery Team Member are assigned to `Delivery AAG-Users-ADP-{programme}-{devlivey project}_TeamMember` AAD group.
- Technical Team Member are assigned to `AAG-Users-ADP-{programme}-{devlivey project}_TechUser` AAD group.
- Delivery Team Admin are assigned to `AAG-Users-ADP-{programme}-{devlivey project}_Admin` AAD group.
- Delivery Programme Admin are assigned to `AAG-Users-ADP-{programme}_Admin` AAD group.
- ADP Admins are assigned to `AAG-User-ADP-PlatformEngineers` AAD group.

By being added to these groups in Azure AD via the ADP Portal, users will be granted the permissions for their role in the ADP Portal.

The permissions for each role in the ADP Portal are detailed below.

### Platform User

ADP Portal Permissions for the Platform User role:

- Access to the ADP Portal.
- Access to the ADP Portal and can be a member of a Delivery Project or Programme Admin.
- Read access to all ALBs, delivery projects, programmes, etc.

### Delivery Team Member

ADP Portal Permissions for the Delivery Team Member role:

- Includes all Platform User permissions.
- Shows up as a Member of teams.

### Technical Team Member

ADP Portal Permissions for the Technical Team Member role:

- Includes all Delivery Team Member permissions.
- Scaffold/create new services for their delivery project (inc. repos).

### Delivery Team Admin

ADP Portal Permissions for the Delivery Team Admin role:

- Includes all Delivery Team Member permissions.
- Has the ability to invite/add members to their Teams as Team member, Technical Team Members, and Technical Team Admins in ADP Portal which it also adds them to the required GitHub team, Azure DevOps project, and Azure AAD groups required for Azure resource access.
- Edit delivery project details in the ADP Portal.

### Delivery Programme Admin

ADP Portal Permissions for the Delivery Programme Admin role:

- Includes all Delivery Team Admin permissions for all delivery projects in the programme.
- Can create new delivery projects in the programme.
- Can edit programme details in the ADP Portal.
- Can invite/add other admins to the programmes they administer.


### ADP Admin

ADP Portal Permissions for the ADP Admin role:
- Full access to the ADP Portal and is admin for all ALBs, delivery projects, programmes, etc.

## Azure DevOps Permissions

!!! note "TODO"
    What permissions are needed in Azure DevOps for each role?


ADP-ALB-ProgrammeName-DeliveryProjectName-Contributors - For Technical Team Members (write access level to the repo)


## GitHub Permissions

GitHub Permissions use GitHub teams to manage permissions. The key teams are as follows:

- Technical Team Member are assigned to `ADP-{programme}-[delivery project]-Contributors` GitHub team.
- Users with Technical Team Member and Delivery Team Admin roles are assigned to `ADP-{programme}-[delivery project]-Admins` GitHub team.
- ADP Admins are assigned to `ADP-Platform-Admins` GitHub team.


!!! info
    Users with Delivery Team Admins, Delivery Programme Admins, or Delivery Team Members roles only will not be given any GutHub permissions. They can add and remove users from their GitHub teams in the ADP Portal by the add/ remove user functionality in the ADP Portal.


!!! info
    By default all repositories are public and can be accessed by anyone. Anyone can clone, fork, and view the repository. However, only members of the GitHub team will be able to push changes to the repository.

### Technical Team Member

Technical Team Members are given the following permissions in GitHub:

- Write access to Delivery Projects GitHub repositories, which will allow triage permissions plus read, clone and push to repositories.

###  Technical Team Member with Delivery Team Admin

Technical Team Members with Delivery Team Admin are given the following permissions in GitHub:

- All permissions of a Technical Team Member.
- Admin access to Delivery Projects GitHub repositories, which will allow full access to their repositories including sensitive and destructive actions.

### ADP Admin

ADP Admins are given the following permissions in GitHub:

- All permissions of a Technical Team Member with Delivery Team Admin.
- Full access to all ADP repositories in the DEFRA GitHub organization.

## Azure Permissions

For Azure permissions we use AAD group to given users the correct level of permissions. There are the key groups are for Azure permissions are as follows:

- Technical Team Member are assigned to `AAG-Users-ADP-{programme}-{delivery project}_TechUser` AAD group.
- ADP Admins are assigned to `AAG-User-ADP-PlatformEngineers` AAD group.

!!! info
    Users with Delivery Team Admins, Delivery Programme Admins, or Delivery Team Members roles only will not be given any Azure permissions. They can add, edit, or remove users from their delivery projects AAD groups in the ADP Portal by the add/ remove user functionality in the ADP Portal.

## Technical Team Member

Technical Team Members are given the following permissions in Azure:
- ...

### Spell out permissions for each group in each of Azure resources, etc

Should this be done here or in an another page?

Resource group
Database

- AAG-Azure-ADP-{programme}-{delivery project}-{environment}-PostgressDB_Reader
- AAG-Azure-ADP-{programme}-{delivery project}-{environment}-PostgressDB_Writer