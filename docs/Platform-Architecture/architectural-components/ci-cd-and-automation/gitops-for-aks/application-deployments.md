---
title: Application Deployments
summary: Application deployments using GitOps
authors:
    - Ken Babigumira
date: 2024-03-14
weight: 1
---

# Application Deployments

**Design Principles**
- Application repositories will contain the application source code, docker files and helm charts to deploy the application.

```
Application-repo
├─ Dockerfile
├─ src/
│  └─ ....
└─ manifests/
   └─ helm/
      ├─ Chart.yaml
      ├─ values.yaml
      └─ templates/
         └─ ....
```
- All application deployments are managed with `HelmRelease`.
- All GitOps environments should use the main branch. Approaches such as a branch per environment have downsides and should be avoided.
- Each environment will have an environment-specific Azure Container Registry that the CI pipeline will push the docker images and artifacts (helm charts etc) to.



## Promoting Changes across Environments

CI/CD pipelines should support both continuous deployment and continuous delivery. 