# ACI Demos

This section is containing Kubernetes Manifest files used to get familiar with [Azure Container Instances](https://azure.microsoft.com/en-us/services/container-instances)

## Overview 

### testingEnvironment + pipeline

The [testingEnvironment.yaml](AciOutburst/testingEnvironment.yaml) is used together with [pipeline.yaml](AciOutburst/pipeline.yaml) in order to demonstrate how to integrate AKS and Azure DevOps.
The Showcase will spin up an Environment in AKS (which will tolerate ACI Outbursting) and give the user the ability to run specific commands in the Environment. 

[testingEnvironment.yaml](AciOutburst/testingEnvironment.yaml) is describing the Kubernetes deployment and will spin up the environment which will be used later in AzureDevOps.

Please be aware that the [testingEnvironment.yaml](AciOutburst/testingEnvironment.yaml) cannot be deployed successfully to Kubernetes on its own because of invalid names of important resources. 
These names will be filled in by Azure DevOps later. If you plan to manual deploy [testingEnvironment.yaml](AciOutburst/testingEnvironment.yaml) you will need to make the following adustments: 

```yaml
...
metadata:
  name: __PodName__ ---> Change to Kubernetes ready name
spec:
  containers:
  - name: __PodName__ ---> Change to Kubernetes ready name
...
```

On the other hand [pipeline.yaml](AciOutburst/pipeline.yaml) describes an [Azure DevOps YAML Pipeline](https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=java%2Cyaml%2Cbrowser%2Ctfs-2018-2).
This pipeline contains three agents jobs and will first setup the needed environment with [testingEnvironment.yaml](AciOutburst/testingEnvironment.yaml) and then execute commands with kubectl in the environment.
If you want to extend these abilities make adjustmens to the following lines:

```yaml
...

...
```

### persistentSample

### runOnce
