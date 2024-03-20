---
title: GitHub App Permissions
summary: GitHub App permissions for Backstage
uri: https://defra.github.io/adp-documentation/Developer-Reference/adp-portal/ongoing-development/github-app-permissions/
authors:
    - Logan Talbot
    - James Coxhead
date: 2024-01-22
---
# GitHub App Permissions

**The minimum permissions that we requires for our ADP GitHub App are:**

- For each repository in Defra GitHub, we require read access to metadata
- For each repository in Defra GitHub, we require read and write access to administration, code, and pull requests

**Please note:**

- _Any other permission assume that is set to "no access"_
- _These set of permissions are required for our short to medium term ADP goal and objectives and we will most likely need to expand the scope as time goes on._

## Repository Permissions

Repository permissions permit access to repositories and related resources.

### Repository Administration

Repository creation, deletion, settings, teams, and collaborators.

#### Why we need this permission

- When Scaffolding new templated services within Backstage/ ADO, we need to creating Repos and setting default permissions.

### Repository Code (Contents)

Repository contents, commits, branches, downloads, releases, and merges.

#### Why we need this permission

- When Scaffolding new templated services within Backstage/ ADO, we need to add code to the repos.
- Create code, commit, and branch in adp-flux-core to allow us to automate flux.

#### Potential Risks

### Repository Metadata  (mandatory)

Search repositories, list collaborators and access repository metadata.

#### Why we need this permission

- Mandatory when creating an GitHub application.

#### Potential Risks

### Pull requests

Pull requests and related comments, assignees, labels, milestones and merges.

#### Why we need this permission

- Within Backstage we need to be able to view pull request of matching services.
- Create pull requests in adp-flux-core to allow us to automate flux.

#### Potential Risks

## Organisation permissions

Organisation permissions permit access to organisation related resources.

_None required at this time_

## Account permissions

These permissions are granted on an individual user basis as part of the User authorization flow.

_None required at this time_

# Potential Issues

## Risky API Permissions

GitHub Apps can request almost any permission from the list of API actions supported by GitHub Apps.

**Possible Remediations:**

- [X] Review GitHub App permissions, selecting only permissions that are required in the short to medium term.
- [X] Testing the GitHub App in sandbox organisation.
- [X] Understanding context of backstage plugins or code implemented.
- [ ] Limit GitHub App permissions to specific repositories that are used by the ADP platform. This will have the side effect of decreasing the effectiveness of the platform for the onboarding of view services. Adding a manual step adding in repos (assumption). We would like to keep it the same scope as CDP which is to have access to all repositories in the Defra Org.

## Comprised App credentials

Leaking or misplaced GitHub App credentials.

**Possible Remediation**

- [X] Following best practices ensuring credentials are secure and stored in a key vault in each of are staging and production ADP Portals ([documentation](https://docs.github.com/en/apps/creating-github-apps/about-creating-github-apps/best-practices-for-creating-a-github-app#secure-your-apps-credentials))
- [X] Following best practices ensuring credentials are secure when developing locally. Ensuring that the credentials are not checked in and stored out of source control a local env.sh file.

# Raised Concerns

## As far as I can tell, your Backstage installation will have a client secret to use the app identity to perform administrative functions?

Answer:
Yes, please see permission break down above with the reasons why we require them.

## So presumably we could only ever have the one instance of Backstage?

“It's not possible to have multiple Backstage GitHub Apps installed in the same GitHub organisation, to be handled by Backstage. We currently don't check through all the registered GitHub Apps to see which ones are installed for a particular repository. We only respect global organisation installs right now.”

Answer:
Should be be able to have multiple instances of backstage within the same GitHub organisation. There may be possible conflicts that may occur with certain backstage plugins. For example, [GitHub Discovery search](https://backstage.io/docs/integrations/github/discovery/) for a catalog-info.yaml are repositories to allow for automatic registering of entities. If backstage 1 and backstage 2 use the defaults GitHub Discovery provide configuration will be pick up the same files as each other. To resolve this it will would be as simple as changing config to find yaml(s) file of different names or paths in backstage 1 or 2. There is a second option which would be to restrict what repositories the GitHub Application has access to.

To aid in remediating this concern, will be change the config where we can to add an "adp" suffix. For example, "adp-catalog-info.yaml".

## My other question is why there’s an installed web hook – I don’t really know what this is doing?

We are not using the web hook at the moment but we may look to support GitHub events in future ([Documentation](https://backstage.io/docs/integrations/github/org/#installation-with-events-support)).

## Key References

- [GitHub App Documentation](https://docs.github.com/en/apps/overview)
- [GitHub Apps Best Practices](https://docs.github.com/en/apps/creating-github-apps/about-creating-github-apps/best-practices-for-creating-a-github-app)
- [Github OAuth Apps Security: How to protect yourself against GitHub/OAuth Apps Supply Chain Attacks](https://www.arnica.io/blog/how-to-protect-yourself-against-github-oauth-apps-supply-chain-attacks)
