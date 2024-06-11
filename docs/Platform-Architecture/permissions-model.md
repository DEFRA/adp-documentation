---
title: ADP Permissions Model
summary: Overview of the permissions model in the Azure Development Platform
uri: https://defra.github.io/adp-documentation/Platform-Architecture/permissions-model/
authors:
    - Logan Talbot
date: 2024-03-14
weight: -11
---

# ADP Permissions Model

This page contains an overview of the roles and permissions within ADP (Azure Development Platform). It outlines the different roles such as Platform User, Technical Team Member, Delivery Team Admin, Delivery Programme Admin, and ADP Admin, along with their respective descriptions and responsibilities. Explains the permissions associated with each role in the ADP Portal, Azure DevOps, and GitHub. It describes how permissions are stored in a database and Azure AD using AAD groups. Users are assigned to specific groups based on their roles, granting them the necessary permissions in the ADP Portal, GitHub, Azure, and Azure DevOps.

## ADP Roles

The table below details the roles in the Platform, their scope and description:


| Role | Scope | Description |
|------|-------|-------------|
| Platform User | Platform | A user of the ADP Platform, who has access to the ADP Portal and can be a member of a Delivery Project or Programme. To do this, they must have a Cloud or DefraGovUK Account. |
| Technical Team Member | Delivery Project | Tech Lead, Tester, Developer, or Architect on the Delivery Project team. |
| Delivery Team Member | Delivery Project | Member of the Delivery Project team. |
| Delivery Team Admin | Delivery Project | Tech lead and/or Delivery Manager for the Delivery Project team. |
| Delivery Programme Admin | Delivery Programme | Administers Delivery Programmes within the ADP Portal. |
| ADP Admin | Platform | ADP Platform Engineering delivery team member. |
| CCoE Engineer | Organization | Cloud Center of Excellence engineer. |
| ADP Service Account | Platform | Service account used by automation within ADP. |

!!! info
    **Please note:** if a user holds multiple roles, they will receive the combined permissions associated with all their roles. This ensures that they have access to all the rights and privileges granted by the most significant role they possess. Essentially, the role with the highest level of permissions takes precedence.

## Portal Permissions

The permissions for the portal are stored both in a database and in Azure AD with the use of AAD groups. The group assignments and naming convention are as follows:

- Delivery Non Technical Team Member are assigned to `Delivery AAG-Users-ADP-{programme}-{delivery project}_NonTechUser` AAD group.
- Technical Team Member are assigned to `AAG-Users-ADP-{programme}-{delivery project}_TechUser` AAD group.
- Delivery Team Admin are assigned to `AAG-Users-ADP-{programme}-{delivery project}_Admin` AAD group.
- Delivery Programme Admin are assigned to `AAG-Users-ADP-{programme}_Admin` AAD group.
- ADP Admins are assigned to `AAG-User-ADP-PlatformEngineers` AAD group.

By being added to these groups in Azure AD via the ADP Portal, users will be granted the permissions for their role in the ADP Portal.

The permissions for each role in the ADP Portal are detailed below.

### Platform User

ADP Portal Permissions for the Platform User role:

- Access to the ADP Portal.
- Can be selected as a Delivery Project team member/admin or Delivery Programme Admin.
- Read access to all ALBs, delivery projects, programmes, etc.

### Delivery Project: Team Member

ADP Portal Permissions for the Delivery Project Team Member role:

- Includes all Platform User permissions.
- Displayed as a Member of assigned Delivery Project teams.

### Delivery Project: Technical Team Member

ADP Portal Permissions for the Technical Team Member role:

- Includes all Delivery Project Team Member permissions.
- Scaffold/create new services for their delivery project (inc. repos).

### Delivery Project: Team Admin

ADP Portal Permissions for the Delivery Team Admin role:

- Includes all Delivery Team Member permissions.
- Has the ability to invite/add users to their Delivery Project team as: Team members, Technical Team Members, Team Admins, and Technical Team Admins via the ADP Portal. The function adding an team member will add them to required GitHub team, Azure DevOps project, and Azure AAD groups required for Azure resource access, depending on new team members roles in the Delivery Project.
- Edit delivery project details in the ADP Portal.

### Delivery Programme Admin

ADP Portal Permissions for the Delivery Programme Admin role:

- Includes all Delivery Team Admin permissions for all Delivery Projects in the programme.
- Can create new Delivery Projects in the programme.
- Can edit programme details for programmes they administrator in the ADP Portal.
- Can invite/add other admins to the programmes they administer.


### ADP Admin

ADP Portal Permissions for the ADP Admin role:
- Full access to the ADP Portal and is admin for all ALBs, delivery projects, programmes, etc.


## GitHub Permissions

GitHub Permissions are assigned and managed using GitHub teams. The following GitHub teams are automatically assigned to each repository owned by a Delivery Project:

- `ADP-{programme}-{delivery project}-Contributors` GitHub team: contains all Delivery Project Technical Team Members
- `ADP-{programme}-{delivery project}-Admins` GitHub team: contains users that have been assigned **both** the Technical Team Member & Delivery Team Admin role for the Delivery Project
- `ADP-Platform-Admins` GitHub team: contains the ADP Admins.

!!! info
    *Please Note:* Users that have not been asssigned the Technical Team Member role for a Delivery Project will not be given any GitHub permissions. Delivery Programme Admins & Delivery Project Team Admins can use the ADP Portal to add and remove users from their GitHub teams in via the add/ remove user functionality.


!!! info
    *Please Note:* By default all repositories are public and can be accessed by anyone. Anyone can clone, fork, and view the repository. However, only members of the GitHub team will be able to push changes to the repository.

### Technical Team Member

Technical Team Members are given the following permissions in GitHub:

- Write access to Delivery Projects GitHub repositories, which will allow triage permissions plus read, clone and push to repositories.

###  Technical Team Member with Delivery Team Admin

Users that have been given **both** the Technical Team Member & Team Admin role within a Delivery Project are given the following permissions in GitHub:

- All permissions of a Technical Team Member.
- Admin access to Delivery Projects GitHub repositories, which will allow full access to their repositories including sensitive and destructive actions.

### ADP Admin

ADP Admins are given the following permissions in GitHub:

- All permissions of a Technical Team Member with Delivery Team Admin.
- Full access to all ADP repositories in the DEFRA GitHub organization.

## Azure Permissions

!!! note "TODO"
    
    TBC

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

## Azure DevOps Permissions

!!! note "TODO"
    
    TBC    


ADP-ALB-ProgrammeName-DeliveryProjectName-Contributors - For Technical Team Members (write access level to the repo)

## Sonar Cloud Permissions

ADP will use Technical Team members GitHub account to assign permissions in SonarCloud. Assuming that this GitHub account has been added to the DEFRA's SonarCloud organisation, ADP will assign their GitHub account to the their Delivery Project's SonarCloud group when they are added to a Delivery Project in the ADP Portal. Giving them access to do the required actions for their Delivery Project within SonarCloud.
 
!!! info

    By default all Sonar Cloud projects are public and can be accessed by anyone in read only mode.

ADP portal creates a SonarCloud user group and permissions template per Delivery Project on creation using the `{Delivery Project Team name}` as the groups name. This group will filter on SonarCloud projects by the Delivery Project's ADP namespace or alias fields. For example if project FCP ACD has a ADP namespace of `fcp-acd` and a alias of `ffc-acd` group will have permissions on Sonar Cloud project starting with `fcp-acd*` or `ffc-acd*` (ffc-acd-frontend, fcp-acd-backend, etc).

!!! warning

    SonarCloud projects that do not include the delivery projects ADP namespace or alias in the name of the project in Sonar Cloud will not be included in the group permissions. An Sonar Cloud Organisation Admin will need to add the service to the group permissions manually.


### Technical Team Member

Each Technical Team Member will be added to the SonarCloud user group for the Delivery Project they are a member of in Sonar Cloud. The permissions for the group are as follows for each service in Sonar Cloud:

- Administer Issues: Change the type and severity of issues, resolve issues as being "fixed", "accepted" or "false-positive" (users also need "Browse" permission).
- Administer Security Hotspots: Resolve a Security Hotspot as reviewed (fixed or safe), reset it as to review (users also need Browse permission).

### ADP Admin

ADP Admins will be able to see all services (SonarCloud projects) created by ADP's automation in Sonar Cloud. These are the permissions for the `ADP` user group in Sonar Cloud as the Sonar Cloud project level:

- Administer Issues: Change the type and severity of issues, resolve issues as being "fixed", "accepted" or "false-positive" (users also need "Browse" permission).
- Administer Security Hotspots: Resolve a Security Hotspot as reviewed (fixed or safe), reset it as to review (users also need Browse permission).
- Administer: Access project settings and perform administration tasks. (Users will also need "Browse" permission)
- Execute Analysis: Ability to get all settings required to perform an analysis (including the secured settings like passwords) and to push analysis results to the SonarCloud server.

### ADP Service Account & ADP SonarCloud Automation

ADP requires these permissions in order to run perform API administration tasks in Sonar Cloud at the organisation level. These permissions are required to create the user groups, permissions templates, and add users to the permissions templates in Sonar Cloud. The permissions are as follows:

- Administer: Allows you to perform any action on both Quality Profiles and Quality Gates.
- Execute Analysis: Allows you to trigger an analysis and to push analysis results to the SonarCloud server.
- Create Project: Allows you to initialize a project and configure its settings before the initial first analysis is performed.
- Administer Organization: Allows you to perform all administrative functions for an organization.

Details of [SonarCloud permissions](https://docs.sonarsource.com/sonarcloud/organizations/managing-permissions/).

Current known Sonar Cloud Web API Actions:

- [Create User Group](https://sonarcloud.io/web_api/api/user_groups/create?deprecated=false) - Create a group. Requires the following permission: 'Administer System'.
- [Search for User](https://sonarcloud.io/web_api/api/users/search?deprecated=false) - Search for users. Requires the following permission: 'Administer System'.
- [Add user to User Group](https://sonarcloud.io/web_api/api/user_groups/add_user?deprecated=false) - Add a user to a group. 'id' or 'name' must be provided. Requires the following permission: 'Administer System'.
- [Create Permissions Template](https://sonarcloud.io/web_api/api/permissions/create_template?deprecated=false) -Create a permission template.Requires the permission 'Administer' on the organization.
- [Update Permissions Template](https://sonarcloud.io/web_api/api/permissions/update_template?deprecated=false) - Update a permission template. Requires the permission 'Administer' on the organization.
- [Add User Group to Permission Template](https://sonarcloud.io/web_api/api/permissions/add_group?deprecated=false) - Add a group to a permission template. Requires the permission 'Administer' on the organization. Giving a group the permission of `codeviewer`, `issueadmin`, `securityhotspotadmin`, `scan`, and `user` to the group added to permissions template.

!!! info

    Not possible to [add new users directly to github organisation](https://community.sonarsource.com/t/add-user-to-organization-via-web-api/19735). User will need to be added to the Sonar Cloud organisation manually by a Sonar Cloud Organisation Admin or allow for [member synchronization on DEFRA GitHub organisation](https://docs.sonarsource.com/sonarcloud/organizations/managing-members/#member-synchronization-on-github).