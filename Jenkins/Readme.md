# Jenkins Server

Creates a fully functional Jenkins and expose the service for Kubernetes Plugin internal and for the Web GUI external via Kubernetes Services.

It uses Azure Disk storage for persistent storage dynamically provisioned by Persistent Volume Claims using the Azure Disk storage class. 

## Prerrequisites

The Azure Disks Storage Class needs to be created and configured.

To do so visit [AzureDiskStorageClass](./../StorageClasses/AzureDisk.yaml)


