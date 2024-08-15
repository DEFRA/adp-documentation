---
title: Repository setup
summary: Flux repository setup for ADP
authors:
    - Ken Babigumira
date: 2024-03-14
weight: 1
---
# Repository Setup

The section below describes how the repositories have been set to handle multiple environments and teams with Flux V2.

There are [different ways of structuring repositories](https://fluxcd.io/flux/guides/repository-structure/) as described in the Flux documentation.

## Key considerations

- Teams will deliver new features and bug fixes using trunk-based development
- A new application repository will be created for each service/application
- The repository structure should support multi-tenancy

## Repositories

Two repositories were created to ensure the separation of core infrastructure and application deployments and manifests.

| Repository        | Purpose                                                                                                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| adp-flux-core     | This will be the main repository used for configuring GitOps.`` It will contain the manifests to deploy the core services to the AKS Cluster e.g. for secrets management |
| adp-flux-services | This is a secondary repository that will be referenced by GitRepositories from the main repository `adp-flux-core`                                                            |

### adp-flux-core structure

The [adp-flux-core](https://github.com/DEFRA/adp-flux-core)  contains the following top directories

- **clusters** directory contains the Flux configuration per cluster.
- **infra** directory contains the environment subfolders each containing base and overlays folders. Overlays are used to minimise duplication. [`Kustomizations`](https://github.com/DEFRA/adp-flux-core/blob/main/infra/snd/01/kustomization.yaml) are used to deploy the core services, such as [Nginx Plus](). The base folder for each environment will contain a list of the core services defined in the `core` folder that should be deployed for a specific environment. Folders [01/02](https://github.com/DEFRA/adp-flux-core/tree/main/infra/snd/01) represent cluster instances within an environment. These overlay folders 01/02 contain environment specific [Kustomize patches](https://github.com/DEFRA/adp-flux-core/blob/main/infra/snd/01/nginx-ingress-values.yaml) that contain the environment specific settings.
- **Core** folder contains the Flux configurations (`HelmRepository` and `HelmRelease`) CRD manifests used for installing the core services
- **Services** folder contains a `GitRepository` and Kustomization per environment that points to a path in the [adp-flux-services](https://github.com/DEFRA/adp-flux-services) repository. For example, the `Kustomization` for `snd/01` will to point to the path `services/environments/snd/01` in the adp-flux-services repository.

```
.
├── clusters                                         # Flux configuration (cluster level) /
│   ├── snd/
│   │   └── 01/02/03/04
│   ├── dev/
│   │   └── 01/02                                           
│   ├── tst/
│   │   └── 01/02
│   ├── pre/
│   │   └── 01/02
│   └── prod/
│       └── 01/02 
├── core                                             # Core services installed in the Cluster/
│   ├── azure-service-operator
│   ├── calico
│   ├── cert-manager
│   ├── grafana
│   ├── nginx-ingress
│   └── /reloader
├── infra                                            # Flux configuration for the core services e.g. Nginx Plus/
│   ├── snd                                          # Contains infra configuration for SND core services /
│   │   ├── base                                     # References 1 or more core services to be deployed
│   │   └── 01/02                                    # Overlay containing the patches, references base
│   ├── dev/
│   │   ├── base
│   │   └── 01/02
│   ├── pre/
│   │   ├── base
│   │   └── 01/02
│   └── prod/
│       ├── base
│       └── 01/02
├── core                                            # Contains the manifests for the core service (HelmRelease, HelmRepository)/
│   ├── nginxplus                                  /
│   │   ├── nginx-ingress-chart.yaml
│   │   └── nginx-ingress-release.yaml
│   └── certmanager
└── services                                        # Flux configuration for business applications/
    ├── gitrepository.yaml                          # GitRepository resource for adp-flux-services
    ├── snd                                         # environment/
    │   └── 01                                      # environment instance number    /
    │       ├── Kustomization.yaml                  # References GitRepository, services.yaml
    │       └── services.yaml                       # This is a Kustomization file that references gitrepository.yaml and specifies the path
    ├── dev/
    │   └── 01  /
    │       ├── Kustomization.yaml
    │       └── services.yaml
    ├── pre/
    │   └── 01  /
    │       ├── Kustomization.yaml
    │       └── services.yaml
    └── prod/
        └── 01  /
            ├── Kustomization.yaml
            └── services.yaml              
```

_You can use the [markdown generator tool](https://tree.nathanfriend.io/?s=(%27optiws!(%27fancy!true~fullPath!false~trailZgSlash!true~rootDot!true)~source!(%27source!%27clustjs_kFluxB%7Bclustj%20level%7D%206snd8%2F03%2F046dev8_*%206tst86pre86prod8%20Wcore_LkCqIZstalled%20Z%20thEClustj6azure-A-opjator6calico6cjt-managj6grafana6U6%2FreloadjWZfra_L9FluxBfXthEcqIe.g.%20NgZx%20Plus6snd_*9CwtaZIZfraBfXSND%20cqIY55kRQ1%20XmorEcqIto%20bEdeployed8559Ovjlay%20cwtaZZg%20thEpatches%2C%20rQbase6devY86preY86prodY8Wcore_L9CwtaZIthEmanifestIfXthEcq%20%7BHelmRelease%2C%20HelmRJ%7D6%20ngZxplus5LLLL6LU-chartM6LU-releaseM6%20cjtmanagjWAs_9FluxBfXbusZesIapplicatiws6gitrJM5LL9GitRJ%20resourcEfXadp-flux-As6snd_k%226*0155*9%22%20ZstancEnumbjL6LVM59RQGitRJ%2C%20AsM6LAsM5LkThiIiIa%20V%20filEthat%20rQgitrJM%20and%20specifieIthEpathW%20dev6*7mlW%20pre6*7mlW%20prod6*7mlLL%27)~vjsiw!%271%27)*%20%205LLLL*6W*701*6LVM6LAs.ya86*01%2F029%23%20AsjviceB%20cwfiguratiw%20Ee%20Is%20JepositoryL**M.yamlQefjenceIUngZx-ZgressVKustomizatiwW%5CnXor%20Y6*baseZin_55Ljerk%209qorEAwon%22envirwment%01%22wqkj_ZYXWVUQMLJIEBA98765*) to update the above folder structure_

### adp-flux-services structure

The [adp-flux-core](https://github.com/DEFRA/adp-flux-core)  contains the following top directories

- **services** directory contains the Flux configurations used for deploying the business applications.

#### Tenants

- Tenants refers to the application teams that are responsible for the development of one or more business applications.
- A unique namespace will be created for each tenant
- All applications owned by a specific tenant on the ADP platform will be deployed to the same tenant namespace

Below is a description of the subfolders inside the services folder.

| Subfolder    | Purpose                                                                                                                                                                                                                       |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| base         | the base folder contains manifests that are common to each tenant e.g. namespace, ResourceQuota and ServiceAccount. These manifests are generic, in that they have variables that can be specified at the time of onboarding. |
| environments | This contains the environment subfolders each containing base and overlays folders. Overlays are used to minimise duplication.`Kustomizations` are used to deploy the business services                                     |
| tenants      | These are the application teams                                                                                                                                                                                               |

```
.
└── services/
    ├── base/
    │   └── namespace.yaml
    ├── environments/
    │   ├── snd/
    │   │   └── 01/
    │   │       └── kustomization.yaml                # references business services
    │   ├── dev/
    │   │   └── 01/02
    │   ├── pre/
    │   │   └── 01/02
    │   └── prod/
    │       └── 01/02  
    └── tenant-a                                      # Team level configuration e.g. namespace, notification/
        ├── kustomization.yaml                        # References common manifests defined in apps/base e.g. namespace.yaml
        ├── tenant-a-service1/
        │   ├── helmrelease.yaml                      # tenant a owns 1 or more services
        │   ├── helmrepository.yaml
        │   ├── snd.yaml                              # environment specific patch for snd environment
        │   ├── dev.yaml                              # environment specific patch for dev environment
        │   ├── pre.yaml                              # environment specific patch for pre environment
        │   └── prod.yaml                             # environment specific patch for prod environment
        └── tenant-b-service1/
            ├── helmrelease.yaml
            ├── helmrepository.yaml
            ├── dev.yaml
            ├── snd.yaml
            ├── pre.yaml
            └── prod.yaml

```

_You can use the [markdown generator tool](https://tree.nathanfriend.io/?s=(%27opKs!(%27fancyN~fullPath!falj~trailingSlashN~rootDotN)~Y(%27Y%279sH8baj4A6H%205s4U4%200148F%20CrObusinesI9s4VGeGod48W8H8E-a77X8CTeam%20level%20c_figuraKQ%2C%20notificaK4FX*CROcomm_%20manifestIdefined%20in%20apps%2FbajQ64E-aM7X%20CE%20a%20ownI1%20qmore%209sJUZ73U5xVZ73V5LeZ73pre5LodZXX83prod54E-bMJV6xU6Le6Lod6H8%27)~versi_!%271%27)*8%203%235%20specific%20patch%20fq4H*5%20envir_ment6.yaml7XX*8%20%209jrviceA%20namespaceBxhelmreC%23%20EtenantFkustomizaKZG4%20W4prH%5CnIs%20JBpository6xKti_LxprM-91Bleaj6N!trueOeferenceIQ%20e.g.AUsndVdevW01%2F02X**Ysource!Z67_onjseqor%20x4*%01xqj_ZYXWVUQONMLKJIHGFECBA9876543*) to update the above folder structure_
