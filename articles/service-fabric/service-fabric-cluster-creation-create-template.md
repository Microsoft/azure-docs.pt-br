---
title: Criar um modelo de cluster de Service Fabric do Azure
description: Saiba como criar um modelo do Resource Manager para um cluster do Service Fabric. Configure segurança, Azure Key Vault e Azure AD (Azure Active Directory) para autenticação de cliente.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260499"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo do Resource Manager do cluster do Service Fabric

Um [cluster do Azure Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto conectado por rede de máquinas virtuais no qual os microsserviços são implantados e gerenciados. Um cluster do Service Fabric em execução no Azure é um recurso do Azure e é implantado, gerenciado e monitorado usando o Resource Manager.  Este artigo descreve como criar um modelo do Resource Manager para um cluster do Service Fabric em execução no Azure.  Quando o modelo estiver concluído, será possível [implantar o cluster no Azure](service-fabric-cluster-creation-via-arm.md).

A segurança de cluster é configurada quando o cluster é configurado pela primeira vez e não poderá ser alterada posteriormente. Antes de configurar um cluster, leia [Cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security]. No Azure, o Service Fabric usa o certificado x509 para proteger o cluster e os pontos de extremidade, autenticar clientes e criptografar dados. O Azure Active Directory também é recomendado para proteger o acesso a pontos de extremidade de gerenciamento. Os locatários e usuários do Microsoft Azure AD deverão ser criados antes da criação do cluster.  Para mais informações, leia [Configurar o Microsoft Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

Antes de implantar um cluster de produção para executar cargas de trabalho de produção, primeiro leia a [Lista de verificação de preparação para produção](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos
Os exemplos de modelo do Resource Manager estão disponíveis nos [Exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Esses modelos podem ser usados como ponto de partida para o modelo de cluster.

Este artigo usa o modelo de exemplo de [cluster seguro de cinco nós][service-fabric-secure-cluster-5-node-1-nodetype] e os parâmetros de modelo. Baixe *azuredeploy.json* e *azuredeploy.parameters.json* no computador e abra os dois arquivos no seu editor de texto favorito.

> [!NOTE]
> Para nuvens nacionais (Azure Governamental, Azure China, Azure Alemanha), você também deve adicionar o seguinte `fabricSettings` para o modelo: `AADLoginEndpoint`, `AADTokenEndpointFormat` e `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Adicionar certificados
Os certificados são adicionados a um modelo do Resource Manager de cluster quando você faz a referência ao Key Vault que contém as chaves de certificado. Adicione esses valores e parâmetros de cofre de chaves em um arquivo de parâmetros de modelo do Resource Manager (*azuredeploy.parameters.jsem*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Adicionar todos os certificados ao conjunto de dimensionamento de máquinas virtuais osProfile
Todos os certificados instalados no cluster devem ser configurados na seção **osProfile** do recurso de conjunto de dimensionamento (Microsoft. Compute/virtualMachineScaleSets). Essa ação instrui o provedor de recursos para instalar o certificado nas VMs. Essa instalação inclui o certificado do cluster e os certificados de segurança de aplicativo que você planeja usar para seus aplicativos:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurar o certificado do cluster do Service Fabric

O certificado de autenticação do cluster tem que ser configurado tanto no recurso de cluster do Service Fabric (Microsoft.ServiceFabric/clusters) quanto na extensão do Service Fabric para conjuntos de dimensionamento de máquinas virtuais no recurso de conjunto de dimensionamento de máquinas virtuais. Essa disposição permite que o provedor de recursos do Service Fabric o configure para autenticação do cluster e autenticação de servidor para pontos de extremidade de gerenciamento.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Adicionar as informações de certificado ao recurso de conjunto de dimensionamento de máquinas virtuais

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Adicionar as informações de certificado ao recurso de cluster do Service Fabric

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicionar a configuração do Azure AD para usar o Azure AD para acesso de cliente

Adicione a as configurações do Microsoft Azure Active Directory a um modelo do Resource Manager de cluster fazendo referência ao cofre de chaves que contém as chaves de certificado. Adicione esses parâmetros e valores do Azure AD em um arquivo de parâmetros de modelo do Resource Manager (*azuredeploy.parameters.jsem*). 

> [!NOTE]
> No Linux, os locatários e usuários do Azure AD devem ser criados antes da criação do cluster.  Para mais informações, leia [Configurar o Microsoft Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Preencha o arquivo de parâmetro com os valores

Por fim, use os valores de saída dos comandos do cofre de chaves e do PowerShell do Azure AD para preencher o arquivo de parâmetros.

Se você planeja usar os módulos do PowerShell do Azure service fabric RM, não é necessário preencher as informações de certificado de cluster. Se você deseja que o sistema para gerar o autoatendimento assinado certificado de segurança de cluster, apenas mantê-los como null. 

> [!NOTE]
> Para que os módulos do RM peguem e preencham esses valores de parâmetros vazia, os nomes de parâmetros devem corresponder aos nomes abaixo

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Se você estiver usando certificados de aplicativo ou estiver usando um cluster existente que você carregou no cofre da chave, será necessário obter essas informações e preenchê-las.

Os módulos do RM não têm a capacidade de gerar a configuração do Azure AD para você, portanto, se você planeja usar o Azure AD para acesso do cliente, precisará preenchê-lo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testar o modelo
Use o seguinte comando do PowerShell para testar o modelo do Resource Manager com um arquivo de parâmetros:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Caso você se depare com problemas e receba mensagens criptografadas, use "-Debug" como uma opção.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

O diagrama a seguir ilustra onde a configuração do cofre de chaves e do Azure AD se encaixa em seu modelo do Resource Manager.

![Mapa de dependências do Resource Manager][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Próximas etapas
Agora que você tem um modelo para o cluster, saiba como [implantar o cluster no Azure](service-fabric-cluster-creation-via-arm.md).  Se você ainda não tiver feito isso, leia a [Lista de verificação de preparação para produção](service-fabric-production-readiness-checklist.md) antes de implantar um cluster de produção.

Para saber mais sobre as propriedades e a sintaxe JSON dos recursos implantados neste artigo, consulte:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
