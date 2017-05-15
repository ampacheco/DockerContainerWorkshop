# Docker Containers Workshop

## Mostrar plantillas ARM

### Descripción General de la Plantilla

```
{
  "schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 
  "contentVersion": "1.0.0.0",
 
  "parameters": { },
 
  "variables": { },
 
  "resources": [ ],
 
  "outputs": { }
}
```

### Plantillas ARM para deployment de Docker usando DC/OS

Plantilla ARM para el Deployment de Docker usando como orchestrator a DC/OS

[Descripción Detallada de la Plantilla](https://docs.microsoft.com/en-us/azure/container-service/container-service-mesos-marathon-ui)

[Repositorio Git](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)


### Plantilla Parámetros
```
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dnsNamePrefix": {
      "value": "GEN-UNIQUE"
    },
    "agentCount": {
      "value": 1
    },
    "agentVMSize": {
      "value": "Standard_A2"
    },
    "linuxAdminUsername": {
      "value": "azureuser"
    },
    "orchestratorType": {
      "value": "DCOS"
    },
    "masterCount": {
      "value": 3
    },
    "sshRSAPublicKey": {
      "value": "GEN-SSH-PUB-KEY"
    }
  }
}
```

### Plantilla Recursos

```(https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-dcos/azuredeploy.json)

### Laboratiorio, hacer el deployment
### Conectarse al Servicio

## Deployments de Plantiallas ARM mediante el Portal

## Deployments de Plantillas ARM, mediante Powershell

## Deployments de Plantillas ARM mediante Azure CLI 2.0

