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
```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dnsNamePrefix": {
      "type": "string",
      "metadata": {
        "description": "Sets the Domain name prefix for the cluster.  The concatenation of the domain name and the regionalized DNS zone make up the fully qualified domain name associated with the public IP address."
      }
    },
    "agentCount": {
      "type": "int",
      "defaultValue": 1,
      "metadata": {
        "description": "The number of agents for the cluster.  This value can be from 1 to 100 (note, for DC/OS clusters you will also get 1 or 2 public agents in addition to these seleted masters)"
      },
      "minValue":1,
      "maxValue":100
    },
    "agentVMSize": {
      "type": "string",
      "defaultValue": "Standard_D2_v2",
      "allowedValues": [
    		"Standard_A0", "Standard_A1", "Standard_A2", "Standard_A3", "Standard_A4", "Standard_A5",
    		"Standard_A6", "Standard_A7", "Standard_A8", "Standard_A9", "Standard_A10", "Standard_A11",
    		"Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4",
    		"Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14",
    		"Standard_D1_v2", "Standard_D2_v2", "Standard_D3_v2", "Standard_D4_v2", "Standard_D5_v2",
    		"Standard_D11_v2", "Standard_D12_v2", "Standard_D13_v2", "Standard_D14_v2",
    		"Standard_G1", "Standard_G2", "Standard_G3", "Standard_G4", "Standard_G5",
    		"Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4",
    		"Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14",
    		"Standard_GS1", "Standard_GS2", "Standard_GS3", "Standard_GS4", "Standard_GS5"
  	  ],
      "metadata": {
        "description": "The size of the Virtual Machine."
      }
    },
    "linuxAdminUsername": {
      "type": "string",
      "defaultValue": "azureuser",
      "metadata": {
        "description": "User name for the Linux Virtual Machines."
      }
    },
    "orchestratorType": {
      "type": "string",
      "defaultValue": "DCOS",
      "allowedValues": [
        "DCOS",
        "Swarm"
      ],
      "metadata": {
        "description": "The type of orchestrator used to manage the applications on the cluster."
      }
    },
    "masterCount": {
      "type": "int",
      "defaultValue": 1,
      "allowedValues": [
        1,
        3,
        5
      ],
      "metadata": {
        "description": "The number of DC/OS masters for the cluster."
      }
    },
    "sshRSAPublicKey": {
      "type": "string",
      "metadata": {
        "description": "Configure all linux machines with the SSH RSA public key string.  Your key should include three parts, for example 'ssh-rsa AAAAB...snip...UcyupgH azureuser@linuxvm'"
      }
    },
    "enableDiagnostics": {
      "type": "bool",
      "defaultValue": false,
      "metadata": {
        "description": "Enable or disable VM diagnostics."
      }
    }
  },
  "variables": {
    "adminUsername":"[parameters('linuxAdminUsername')]",
    "agentCount":"[parameters('agentCount')]",
    "agentsEndpointDNSNamePrefix":"[concat(parameters('dnsNamePrefix'),'agents')]",
    "agentVMSize":"[parameters('agentVMSize')]",
    "masterCount":"[parameters('masterCount')]",
    "mastersEndpointDNSNamePrefix":"[concat(parameters('dnsNamePrefix'),'mgmt')]",
    "orchestratorType":"[parameters('orchestratorType')]",
    "sshRSAPublicKey":"[parameters('sshRSAPublicKey')]",
    "enableDiagnostics":"[parameters('enableDiagnostics')]"
  },
  "resources": [
    {
      "apiVersion": "2016-09-30",
      "type": "Microsoft.ContainerService/containerServices",
      "location": "[resourceGroup().location]",
      "name":"[concat('containerservice-',resourceGroup().name)]",
      "properties": {
        "orchestratorProfile": {
          "orchestratorType": "[variables('orchestratorType')]"
        },
        "masterProfile": {
          "count": "[variables('masterCount')]",
          "dnsPrefix": "[variables('mastersEndpointDNSNamePrefix')]"
        },
        "agentPoolProfiles": [
          {
            "name": "agentpools",
            "count": "[variables('agentCount')]",
            "vmSize": "[variables('agentVMSize')]",
            "dnsPrefix": "[variables('agentsEndpointDNSNamePrefix')]"
          }
        ],
        "diagnosticsProfile": {
          "vmDiagnostics" : {
              "enabled": "[variables('enableDiagnostics')]"
          }
        },
        "linuxProfile": {
          "adminUsername": "[variables('adminUsername')]",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[variables('sshRSAPublicKey')]"
              }
            ]
          }
        }
      }
    }
  ],
  "outputs": {
    "masterFQDN": {
      "type": "string",
      "value": "[reference(concat('Microsoft.ContainerService/containerServices/', 'containerservice-', resourceGroup().name)).masterProfile.fqdn]"
    },
    "sshMaster0": {
      "type": "string",
      "value": "[concat('ssh ', variables('adminUsername'), '@', reference(concat('Microsoft.ContainerService/containerServices/', 'containerservice-', resourceGroup().name)).masterProfile.fqdn, ' -A -p 2200')]"
    },
    "agentFQDN": {
      "type": "string",
      "value": "[reference(concat('Microsoft.ContainerService/containerServices/', 'containerservice-', resourceGroup().name)).agentPoolProfiles[0].fqdn]"
    }
  }
}
```

### Laboratiorio, hacer el deployment
### Conectarse al Servicio

## Deployments de Plantiallas ARM mediante el Portal

## Deployments de Plantillas ARM, mediante Powershell

```
# Autneticarse a la Cuenta
Login-AzureRmAccount

# Crear Grupo de Recurso
New-AzureRmResourceGroup 
  -Name ExampleResourceGroup 
  -Location "EastUS"

# Hacer el Deployment del Recuros
New-AzureRmResourceGroupDeployment 
  -Name ExampleDeployment 
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json 
  -storageAccountType Standard_GRS

```
## Deployments de Plantillas ARM mediante Azure CLI 2.0

```
az login
az account set --name $subscriptionId
az group create --name $resourceGroupName --location $resourceGroupLocation
az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath

```



