---
title: Criar um hub de eventos com a captura habilitada – Hubs de Eventos do Azure | Microsoft Docs
description: Criar um namespace de Hubs de Eventos do Azure com um hub de eventos e habilitar a Captura usando um modelo do Azure Resource Manager
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1b7ce00c1db0866b1965ed6d1bba31aa28f8bc24
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304929"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Criar um namespace com o hub de eventos e habilitar a Captura usando um modelo

Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace de [Hubs de Eventos](./event-hubs-about.md), com uma instância de hub de eventos e também habilita o [recurso Captura](event-hubs-capture-overview.md) no hub de eventos. O artigo descreve como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Este artigo também mostra como especificar que os eventos sejam capturados em Blobs de Armazenamento do Azure ou em um Azure Data Lake Store com base no destino escolhido.

Para saber mais sobre a criação de modelos, consulte [Criação de modelos do Azure Resource Manager][Authoring Azure Resource Manager templates]. Para que as propriedades e a sintaxe JSON sejam usadas no modelo, consulte [Tipos de recursos Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Para saber mais sobre as práticas e os padrões de convenções de nomenclatura de Recursos do Azure, confira [Convenções de nomenclatura de funcionalidade do Azure][Azure Resources naming conventions].

Para obter os modelos completos, clique nos seguintes links do GitHub:

- [Hub de eventos e habilitar a Captura no modelo de Armazenamento][Event Hub and enable Capture to Storage template] 
- [Hub de eventos e habilitar a Captura no modelo do Azure Data Lake Store][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Azure Quickstart Templates] e procure por Hubs de Eventos.
> 
> 

## <a name="what-will-you-deploy"></a>O que você implantará?

Com esse modelo, você implanta um namespace de Hub de Eventos com um hub de eventos e também habilita a [Captura dos Hubs de Eventos](event-hubs-capture-overview.md). A Captura dos Hubs de Eventos permite que você forneça automaticamente os dados de streaming em Hubs de Eventos para o Armazenamento de Blobs do Azure ou Azure Data Lake Store, dentro de um período especificado ou do intervalo de tamanho de sua preferência. Clique no botão abaixo para habilitar a Captura de Hubs de Eventos no Armazenamento do Azure:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Clique no botão abaixo para habilitar a Captura de Hubs de Eventos no Azure Data Lake Store:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

O nome do namespace Hubs de Evento a criar.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

O nome do hub de eventos criado no namespace Hubs de Eventos.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

O número de dias para manter as mensagens no hub de eventos. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

O número de partições a serem criadas no hub de eventos.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Habilite a Captura no hub de eventos.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

O formato de codificação que você especifica para serializar os dados do evento.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

O intervalo de tempo no qual a Captura de Hubs de Eventos inicia a captura de dados.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
O intervalo de tamanho no qual a Captura inicia a captura de dados.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

O formato de nome usado pela Captura de Hubs de Eventos para gravar os arquivos Avro. Observe que um formato de nome de Captura deve conter os campos `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` e `{Second}`. Eles podem ficar organizados em qualquer ordem, com ou sem delimitadores.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

A versão da API do modelo.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Use os parâmetros a seguir se você escolher o Armazenamento do Azure como destino.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

A Captura exige uma ID de recurso da conta de Armazenamento do Azure para habilitar a captura na sua conta de armazenamento desejada.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

O contêiner de blob no qual deseja capturar os dados de evento.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Use os parâmetros a seguir se você escolher o Azure Data Lake Store Gen1 como o destino. Você deve definir permissões no caminho do Data Lake Store no qual deseja capturar o evento. Para definir permissões, confira [Capturar dados no Azure Data Lake Storage Gen1](event-hubs-capture-enable-through-portal.md#capture-data-to-azure-data-lake-storage-gen-1).

### <a name="subscriptionid"></a>subscriptionId

ID de assinatura para o namespace de Hubs de Eventos e o Azure Data Lake Store. Os dois recursos devem estar na mesma ID de assinatura.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

O nome do Azure Data Lake Store para os eventos capturados.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

O caminho da pasta de destino para os eventos capturados. Esta é a pasta em seu Data Lake Store na qual os eventos serão enviados durante a operação de captura. Para definir permissões nessa pasta, consulte [Usar o Azure Data Lake Store para capturar dados de Hubs de Eventos](../data-lake-store/data-lake-store-archive-eventhub-capture.md).

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Recursos a serem implantados no Armazenamento do Azure como destino dos eventos capturados

Cria um namespace do tipo **EventHub** com um hub de eventos, e também habilita a Captura para o Armazenamento de Blobs do Azure.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Recursos a serem implantados no Azure Data Lake Store como destino

Cria um namespace do tipo **EventHub**, com um hub de eventos, e também habilita a Captura para o Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Você pode habilitar ou desabilitar a emissão de arquivos vazios quando nenhum evento ocorre durante a janela de Captura usando a propriedade **skipEmptyArchives**. 

## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Implante o modelo para habilitar a captura de Hubs de Eventos no Armazenamento do Azure:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Implante o modelo para habilitar a captura de Hubs de Eventos no Azure Data Lake Store:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>CLI do Azure

Armazenamento de Blobs do Azure como destino:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Azure Data Lake Store como destino:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Próximas etapas

Você também pode configurar a Captura de Hubs de Eventos por meio do [portal do Azure](https://portal.azure.com). Para saber mais, confira [Habilitar a Captura de Hubs de Eventos usando o portal do Azure](event-hubs-capture-enable-through-portal.md).

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](./event-hubs-about.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.yml)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
