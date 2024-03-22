---
title: Service Versioning Strategy
summary: Platform service versioning strategy.
uri: https://defra.github.io/adp-documentation/Platform-Strategy/service-versioning-strategy/
authors:
    - Dan Rozkowski
date: 2024-03-14
---
# Platform service versioning strategy.

This document outlines a two-phase versioning strategy for services on ADP with the goal to support ephemeral environments by phrase 2. In Phase 1, before ephemeral environments, feature branch builds fetch the version from the main branch's package.json. If the versions are the same, an error is thrown; if the feature branch version is higher, it's tagged with alpha and the build ID. The main branch version is pushed to ACR on deployment after merging into main, taking precedence over all feature (alpha) candidates of the same major/minor/patch. In Phase 2, once ephemeral environments are in place, the process remains the same for feature branches. For PR builds, if the package.json is not updated, a validation error is thrown; if it's updated, it's tagged with a release candidate (-RC) and the build ID. The main branch version still takes precedence over all feature (alpha & RC) candidates. The Build ID is unique and automatically increases on every CI on every image requested to be deployed. Users must increment Major, Minor, or Patch at least once on a feature branch build or PR build, and to merge into main for a successful release and validation. Once ephemeral environments are delivered, PR's and Feature releases will have their own dedicated infrastructure.

## Phase 1 - requirements (before ephemeral environments)

**Feature branch build/deploys**

- Get version from Main branch package.json for the repo (e.g. 4.2.30)
  - if main and feature branch are the same version (M/M/P) then:
    - throw validation error message that "increment is invalid. User must increase the package.json version"
  - if main and feature branch version are not same (i.e user has supplied it and increased Major, Minor or Patch) and Feature Branch > Main branch then:
    - tag it with alpha and buildID which becomes 4.2.31-alpha.511210 and respect users major/minor/patch
- push this version to ACR when a deploy is requested.

**PR builds/deploys**

No change for phase 1. Above requirements as is. User's merge (feature branch) version must be above main at all times.

**Main branch**

- 4.2.31 (example)
- push this version to ACR on deployment after merge into main
- the main branch version is the primary and priority version which takes precedence over and above all feature (alpha ) candidates of the same major/minor/patch.

## Phase 2 - requirements once ephemeral environments are in place!

**Feature branch build/deploys**

- Get version from Main branch package.json for the repo (e.g. 4.2.30)
  - if main and feature branch are the same version (M/M/P) then:
    - throw validation error message that "increment is invalid. User must increase the package.json version"
  - if main and feature branch version are not same (i.e user has supplied it and increased Major, Minor or Patch) and Feature Branch > Main branch then:
    - tag it with alpha and buildID which becomes 4.2.31-alpha.511210 and respect users major/minor/patch
- push this version to ACR

**PR builds/deploys**

- if package.json is not updated in team repo then throw validation message: "increment Invalid '4.2.30' -> '4.2.30'. Please upgrade" (as above requirement error too)
- if package.json is updated (4.2.31) then tag it with release candidate (-RC) and buildID which becomes 4.2.31-rc.511211
- push this version to ACR on deployment

**Main branch**

- 4.2.31
- push this version to ACR on deployment after merge into main
  - the main branch version is the primary and priority version which takes precedence over and above all feature (alpha & RC) candidates of the same major/minor/patch.

**Guidance/context**


The Build ID is unique and is the ADO Pipeline build ID. It automatically increases on every CI on every image you request to be deployed (feature deployment). Users must increment Major, Minor or Patch at least once, on a Feature branch build or PR build, and to merge into main for a successful release and validation. Main takes priority over Alpha and RC candidates of the same major/minor/patch. Once ephemeral environments are delivered, PR's and Feature releases will have it's own dedicated infrastructure. In the interim, they will overwrite the "main" version.
