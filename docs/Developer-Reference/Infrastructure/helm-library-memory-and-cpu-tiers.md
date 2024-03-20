---
title: Helm Library - Memory and CPU Tiers
summary: Overview of the Helm Library - Memory and CPU Tiers
uri: https://defra.github.io/adp-documentation/Developer-Reference/Infrastructure/helm-library-memory-and-cpu-tiers/
authors:
    - xxx
date: 2024-03-14
weight: 1
---
# Helm Library - Memory and CPU Tiers

We have now implemented an abstraction layer within the adp-helm-library that allows the dynamic allocation of memory and CPU resources based on the memory and cpu tier provided in the values.yaml file.

The new memory and cpu tier values are in the below table:

| TIER   | CPU-REQUEST | CPU-LIMIT | MEMORY-REQUEST | MEMORY-LIMIT |
| ------ | ----------- | --------- | -------------- | ------------ |
| S      | 50m         | 50m       | 50Mi           | 50Mi         |
| M      | 100m        | 100m      | 100Mi          | 100Mi        |
| L      | 150m        | 150m      | 150Mi          | 150Mi        |
| XL     | 200m        | 200m      | 200Mi          | 200Mi        |
| XXL    | 300m        | 600m      | 300Mi          | 600Mi        |
| CUSTOM | <?>         | <?>       | <?>            | <?>          |

**Instructions**

The following values can optionally be set in a `values.yaml` to select the required CPU and Memory for a container:

```
container:
  memCpuTier: <string S|M|L|XL|XXL|CUSTOM>
  requestMemory: <string - REQUIRED if memCpuTier is CUSTOM>
  requestCpu: <string - REQUIRED if memCpuTier is CUSTOM>
  limitMemory: <string - REQUIRED if memCpuTier is CUSTOM>
  limitCpu: <string - REQUIRED if memCpuTier is CUSTOM>
```

example 1 - select an Extra Large (XL) tier:

```
container:
  memCpuTier: XL
```

example 2 - select an Small (S) tier:

```
container:
  memCpuTier: S
```

example 3 - select custom values and provide your own values if the TIER sizes don't fit your requirements.

```
container:
  memCpuTier: CUSTOM
  requestMemory: 10Mi
  requestCpu: 10m
  limitMemory: 200Mi
  limitCpu: 200m
```

example 4 - The default is Medium (M).  If this works for you then you don't need to pass a memCpuTier.

```
container: {}
```

**NOTE:**
If you do not add a 'memCpuTier' then the Tier will default to 'M'

**NOTE:**
You can also choose CUSTOM and provide your own values if the TIER sizes don't fit your requirements.
If you choose CUSTOM, requestMemory, requestCpu, limitMemory and limitCpu are required.

**IMPORTANT:**
Your team namespace will be given a fixed amount of resources via ResourceQuotas.
Once your cumulative resource request total passes the assigned quota on your namespace, all further deployments will be unsuccessful.  If you require an increase to your ResourceQuota, you will need to raise a request via the ADP team.  It's important you monitor the performance of your application and adjust pod requests and limits accordingly.  Please choose the appropriate cpu and memory tier for your application or provide custom values for your CPU and Memory requests and limits.

**References**

https://learn.microsoft.com/en-us/azure/aks/developer-best-practices-resource-management

https://learn.microsoft.com/en-us/azure/aks/operator-best-practices-scheduler#enforce-resource-quotas
