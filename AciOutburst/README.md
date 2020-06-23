# ACI Demos

This section is containing Kubernetes Manifest files used to get familiar with [Azure Container Instances](https://azure.microsoft.com/en-us/services/container-instances)

## Overview 

### testingEnvironment + pipeline

The [testingEnvironment.yaml](testingEnvironment.yaml) is used together with [pipeline.yaml](pipeline.yaml) in order to demonstrate how to integrate AKS and Azure DevOps.
The Showcase will spin up an Environment in AKS (which will tolerate ACI Outbursting) and give the user the ability to run specific commands in the Environment. 

[testingEnvironment.yaml](testingEnvironment.yaml) is describing the Kubernetes deployment and will spin up the environment which will be used later in AzureDevOps.

Please be aware that the [testingEnvironment.yaml](testingEnvironment.yaml) cannot be deployed successfully to Kubernetes on its own because of invalid names of important resources. 
These names will be filled in by Azure DevOps later. If you plan to manual deploy [testingEnvironment.yaml](testingEnvironment.yaml) you will need to make the following adustments: 

```yaml
...
metadata:
  name: __PodName__ ---> Change to Kubernetes ready name
spec:
  containers:
  - name: __PodName__ ---> Change to Kubernetes ready name
...
```

On the other hand [pipeline.yaml](pipeline.yaml) describes an [Azure DevOps YAML Pipeline](https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=java%2Cyaml%2Cbrowser%2Ctfs-2018-2).
This pipeline contains three agents jobs and will first setup the needed environment with [testingEnvironment.yaml](testingEnvironment.yaml) and then execute commands with kubectl in the environment.

If you want to extend these abilities make adjustmens to the following lines:

```yaml
... [Line 47 - 54]
# Execute the command in the existing pod with kubectl exec --> Make sure to link your Kubernetes Service connection: https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#create-a-service-connection
  - task: Kubernetes@1
    displayName: 'kubectl exec'
    inputs:
      kubernetesServiceEndpoint: 'KubeTest-AksDemoCluster-default-1592853187390'
      namespace: default
      command: exec
      arguments: '$(PodName) -- /bin/bash -c echo Starting Test procedure; sleep 15; echo Done' <----- Add your commands here

...
```

### persistentSample

Will create a simple nginx container as ACI instance and wont exit.

### runOnce

Will create a simple Job as an ACI instance which will be alive for a specific amount of time. It will exist afterwards. 
