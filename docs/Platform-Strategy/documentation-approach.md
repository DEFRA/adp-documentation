---
title: Documentation Approach
summary: Details of how we are going to document ADP at a high level.
uri: https://defra.github.io/adp-documentation/Platform-Strategy/documentation-approach/
authors:
    - Logan Talbot
date: 2024-02-16
weight: 1
---

# Documentation Approach

For ADP there is going to be two data sources for documentation one that can be external (ADP Documentation) and another that will be internal (ADP Documentation Internal) both will be contributed to on GitHub via a GitHub Git repository. Our approach is to fellow GDS's Service standard of "Make new source code open" for our documentation making most of our documentation open to the public allowing it to be easily viewed by third parties. Making it available for reuse on an open licence while still keeping ownership of the intellectual property, enabling across government collaboration and ease of support for existing and future projects with Defra. For the minority of documentation which is classified as possible sensitive information will be available via ADP Internal Documentation.

Diagram of our approach:

![documentation-approach](../images/documentation-approach.jfif)

Explanation:

* Tech User - normally a developer, Tech Lead, or Solution Architect will be able to access both internal and external documentation. Either from the ADP Portal or from a GitHub pages website. Tech users can view and extend the documentation going directly to a GitHub repository and submitting a pull request.
* Non-tech User - Project Manager, Business Analyst, etc will have access to internal and external documentation via the ADP Portal. For the external documentation they will still have access to this GitHub page but not the external documentation's GitHub page. As non-tech users will require a GitHub account that is in Defra's GitHub Organisation to access it.
* 3rd party - Member of the public, possible Defra supplier, member of a non-Defra government department, etc will have access to External documentation via GitHub pages website.
* ADP Team - Will have access to all. Will be the main contributors and approvers of internal/ external documentation. ADP team will add additional documentation to the README.md of the ADP GitHub repositories. On commit to the main branch this will be copied over the internal/ external documentation repositories allowing ADP customers to reduce the amount of places they need to look for documentation increasing their productivity for learning and use when on the ADP.
* Automated deployments - On commit to main for both internal and external documentation repositories, deployment pipelines will be ran to deploy the documentation to GitHub pages and to ADP Portal's documentation store.

**ADP Documentation (External)**

Portal Link: [https://portal.snd1.adp.defra.gov.uk/docs/default/component/adp-documentation](https://portal.snd1.adp.defra.gov.uk/docs/default/component/adp-documentation)

GitHub Pages: [https://defra.github.io/adp-documentation](https://defra.github.io/adp-documentation) (public)

GitHub Repository: [https://github.com/DEFRA/adp-documentation](https://github.com/DEFRA/adp-documentation)

What will be stored here:

* What is ADP? - Introduction to ADP, Pros, cons, limitations, Is your Defra project right for ADP?
* Getting Started - Guides to enables ADP customers (users, projects, programmes) to get started quickly on ADP.
* How to guides -  Guides on how to do development functions when developing on ADP, focused on the tech user.
* Platform Strategy - details of the ADP Platforms Strategies  (current and future).
* Migrate to ADP - Step by step instructions on how to migrate your existing Defra project & services over to ADP.
* Developer Reference - Detailed reference material required to enable tech users to work with ADP. ADP repositories README's will be copied here.
* Platform Architecture - Fairly high level details of ADP Architecture. Enough detail that another Platform team could not implement ADP else where.

**ADP **Documentation** Internal**

Portal Link: [https://portal.snd1.adp.defra.gov.uk/docs/default/component/adp-documentation-internal](https://portal.snd1.adp.defra.gov.uk/docs/default/component/adp-documentation-internal)

GitHub Pages: [https://defra.github.io/adp-documentation-internal/](https://defra.github.io/adp-documentation-internal/)

GitHub Repository: [https://github.com/DEFRA/adp-documentation-internal](https://github.com/DEFRA/adp-documentation-internal) (private)

What will be stored here:

* ADP Internal - Internal information that should not be accessible to the public.
* Internal Architecture Details - Architecture information that should not be accessible to the public.
* ADP Runbooks - step by step instructions that are required as part of the change management process in order to release to production.
