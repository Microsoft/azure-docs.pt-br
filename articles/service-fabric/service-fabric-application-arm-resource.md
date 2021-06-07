---
title: Implantar e atualizar com Azure Resource Manager
description: Saiba como implantar aplicativos e serviços em um cluster do Service Fabric usando um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ed6bc7d96cb3ea0934929e6543c5e637a9f42c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930830"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Gerenciar aplicativos e serviços como recursos do Azure Resource Manager

É possível implantar aplicativos e serviços em seu cluster do Service Fabric por meio do Azure Resource Manager. Isso significa que, em vez de implantar e gerenciar aplicativos por meio do PowerShell ou da CLI após ter que aguardar o cluster estar pronto, agora é possível expressar aplicativos e serviços em JSON e implantá-los no mesmo modelo do Resource Manager que seu cluster. Todo o processo de registro, provisionamento e implantação de aplicativos acontece em uma etapa.

Essa é a maneira recomendada para implantar qualquer configuração, governança ou aplicativos de gerenciamento de cluster exigidos em seu cluster. Isso inclui o [Aplicativo de orquestração de patches](service-fabric-patch-orchestration-application.md), Watchdogs ou aplicativos que precisam estar em execução em seu cluster antes que outros aplicativos ou serviços sejam implantados. 

Quando aplicável, gerencie seus aplicativos, como recursos do Resource Manager para melhorar:
* A trilha de auditoria: o Resource Manager audita toda operação e mantém um *Log de atividades* que pode ajudar a rastrear alterações feitas nesses aplicativos e no seu cluster.
* Controle de acesso baseado em função do Azure (RBAC do Azure): gerenciar o acesso a clusters, bem como aplicativos implantados no cluster, pode ser feito por meio do mesmo modelo do Resource Manager.
* O Azure Resource Manager (por meio do Portal do Azure) se torna uma loja única para gerenciar o cluster e as implantações de aplicativos críticos.

O snippet a seguir mostra os diferentes tipos de recursos que podem ser gerenciados por meio de um modelo:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

## <a name="add-a-new-application-to-your-resource-manager-template"></a>Adicione um novo aplicativo ao seu modelo do Resource Manager

1. Prepare o modelo do Resource Manager do cluster para implantação. Consulte [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter mais informações sobre isso.
2. Pense em alguns aplicativos que você planeja implantar no cluster. Há algum que estará sempre executando esses outros aplicativos nos quais ele pode assumir dependências? Você planeja implantar qualquer governança de cluster ou aplicativos de configuração? Esses tipos de aplicativos são mais bem gerenciados por meio de um modelo do Resource Manager, como discutido acima. 
3. Depois de descobrir quais aplicativos você deseja implantar dessa maneira, os aplicativos precisam ser empacotados, compactados e colocados em um compartilhamento de armazenamento. O compartilhamento precisa ser acessado por meio de um ponto de extremidade REST para que o Azure Resource Manager consuma durante a implantação. Consulte [criar uma conta de armazenamento](service-fabric-concept-resource-model.md#create-a-storage-account) para obter detalhes.
4. Em seu modelo do Resource Manager, embaixo da declaração do seu cluster, descreva as propriedades de cada aplicativo. Essas propriedades incluem a contagem de instâncias e de réplicas e quaisquer cadeias de dependência entre recursos (outros aplicativos ou serviços). Observe que isso não substitui os manifestos do aplicativo nem do serviço, mas descreve algumas coisas que estão neles como parte do modelo do Resource Manager do cluster. Veja um exemplo de modelo que inclui a implantação de um serviço sem estado *Service1* e um serviço com estado *Service2* como parte do *Application1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > Consulte o Service Fabric [Azure Resource Manager referência](/azure/templates/microsoft.servicefabric/clusters/applicationtypes) para localizar o uso e detalhes sobre as propriedades de modelo individuais.

5. Implante! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Remover Service Fabric recurso de aplicativo do provedor de recursos
O seguinte irá disparar o pacote do aplicativo para ser desprovisionado do cluster, e isso limpará o espaço em disco usado:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
A simples remoção de Microsoft. infabric/clusters/aplicativo do modelo do ARM não desprovisionará o aplicativo

>[!NOTE]
> Depois que a remoção for concluída, você não verá mais a versão do pacote no SFX ou no ARM. Não é possível excluir o recurso de versão do tipo de aplicativo com o qual o aplicativo está sendo executado; O ARM/SFRP impedirá isso. Se você tentar desprovisionar o pacote em execução, o runtime do it o impedirá.


## <a name="manage-an-existing-application-via-resource-manager"></a>Gerenciar um aplicativo existente por meio do Resource Manager

Se seu cluster já estiver operante e alguns aplicativos que você gostaria de gerenciar como recursos do Resource Manager já estiverem implantados nele, em vez de remover os aplicativos e implantá-los novamente, será possível usar uma chamada PUT usando as mesmas APIs para que os aplicativos sejam confirmados como recursos do Resource Manager. Para obter informações adicionais, consulte o [que é o modelo de recurso de aplicativo Service Fabric?](./service-fabric-concept-resource-model.md)

> [!NOTE]
> Para permitir que uma atualização de cluster ignore aplicativos não íntegros, o cliente pode especificar “maxPercentUnhealthyApplications: 100” na seção “upgradeDescription/healthPolicy”; descrições detalhadas para todas as configurações estão na [Documentação da política de atualização de cluster da API REST do Service Fabric](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Próximas etapas

* Use a [CLI do Service Fabric](service-fabric-cli.md) ou o [PowerShell](service-fabric-deploy-remove-applications.md) para implantar outros aplicativos em seu cluster. 
* [Atualize seu cluster do Service Fabric](service-fabric-cluster-upgrade.md)
