---
title: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure usando um modelo do Azure Resource Manager
description: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure usando um modelo do Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621421"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Tutorial: Usar um modelo do Azure Resource Manager para configurar o roteamento de mensagens do Hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Roteamento de mensagem

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Baixar o arquivo de parâmetros e o modelo

Para a segunda parte deste tutorial, você pode baixar e executar um aplicativo do Visual Studio para enviar mensagens ao Hub IoT. Há uma pasta nesse download que contém o arquivo de parâmetros e o modelo do Azure Resource Manager, bem como os scripts do PowerShell e CLI do Azure.

Baixe os [Exemplos de C# do IoT do Azure](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) agora mesmo. Descompacte o arquivo master.zip. O arquivo de parâmetros e o modelo do Resource Manager estão em /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ as **template_iothub.json** e **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Criar seus recursos

Você vai usar um modelo do Azure RM (Resource Manager) para criar todos os seus recursos. Pode-se executar algumas linhas de scripts da CLI do Azure e do PowerShell de cada vez. Um modelo do RM é implantado em uma única etapa. Este artigo mostra as seções separadamente para ajudá-lo a entender cada uma delas. Em seguida, mostrará como implantar o modelo e criar o dispositivo virtual para teste. Depois que o modelo for implantado, você poderá exibir a configuração de roteamento de mensagens no portal.

Há vários nomes de recurso que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para facilitar a nomeação dos recursos, os nomes de recurso são configurados para acrescentar um valor alfanumérico aleatório gerado com base na data e hora atuais. 

Se você examinar o modelo, verá em que local as variáveis estão configuradas para esses recursos que usam o parâmetro passado e concatenam *randomValue* para o parâmetro. 

A seção a seguir explica os parâmetros usados.

### <a name="parameters"></a>parâmetros

A maioria desses parâmetros tem valores padrão. Os que terminam com **_in** são concatenados com *randomValue* para torná-los globalmente exclusivos. 

**randomValue**: esse valor é gerado com base na data/hora atual em que você implanta o modelo. Esse campo não está no arquivo de parâmetros, pois é gerado no próprio modelo.

**subscriptionId**: esse campo é predefinido para a assinatura na qual você está implantando o modelo. Esse campo não está no arquivo de parâmetros, pois ele é definido para você.

**IoTHubName_in**: esse campo é o nome base do Hub IoT, que é concatenado com randomValue para ser globalmente exclusivo.

**location**: esse campo é a região do Azure na qual você está implantando, como "Oeste dos EUA".

**consumer_group**: esse campo é o grupo de consumidores definido para mensagens recebidas por meio do ponto de extremidade de roteamento. Ele é usado para filtrar os resultados no Azure Stream Analytics. Por exemplo, há o fluxo total, onde você obtém tudo ou, se estiver recebendo dados com consumer_group definido como **Contoso**, você poderá configurar um fluxo do Azure Stream Analytics (e um relatório do Power BI) para mostrar somente essas entradas. Esse campo é usado na parte 2 deste tutorial.

**sku_name**: esse campo é o dimensionamento do Hub IoT. Esse valor deve ser S1 ou superior; uma camada de serviço gratuita não funciona para este tutorial, pois não permite vários pontos de extremidade.

**sku_units**: esse campo combina com **sku_name** e é o número de unidades do Hub IoT que pode ser usado.

**d2c_partitions**: esse campo é o número de partições usadas para o fluxo de eventos.

**storageAccountName_in**: esse campo é o nome da conta de armazenamento a ser criada. As mensagens são roteadas para um contêiner na conta de armazenamento. Esse campo é concatenado com randomValue para torná-lo globalmente exclusivo.

**storageContainerName**: esse campo é o nome do contêiner no qual as mensagens roteadas para a conta de armazenamento são armazenadas.

**storage_endpoint**: esse campo é o nome para o ponto de extremidade de conta de armazenamento usado pelo roteamento de mensagens.

**service_bus_namespace_in**: esse campo é o nome do namespace do Barramento de Serviço a ser criado. Esse valor é concatenado com randomValue para torná-lo globalmente exclusivo.

**service_bus_queue_in**: esse campo é o nome da fila do Barramento de Serviço usada para roteamento de mensagens. Esse valor é concatenado com randomValue para torná-lo globalmente exclusivo.

**AuthRules_sb_queue**: esse campo consiste nas regras de autorização para a fila do barramento de serviço, usado para recuperar a cadeia de conexão para a fila.

### <a name="variables"></a>variáveis

Esses valores são usados no modelo e derivados principalmente de parâmetros.

**queueAuthorizationRuleResourceId**: esse campo é a ResourceId da regra de autorização para a fila do Barramento de Serviço. ResourceId, por sua vez, é usada para recuperar a cadeia de conexão para a fila.

**iotHubName**: esse campo é o nome do Hub IoT depois de ter randomValue concatenado. 

**storageAccountName**: esse campo é o nome da conta de armazenamento depois de ter randomValue concatenado. 

**service_bus_namespace**: esse campo é o namespace depois de ter randomValue concatenado.

**service_bus_queue**: esse campo é o nome da fila do Barramento de Serviço depois de ter randomValue concatenado.

**sbVersion**: a versão da API do Barramento de Serviço a ser usada. Neste caso, é "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Recursos: contêiner e conta de armazenamento

O primeiro recurso criado é a conta de armazenamento, juntamente com o contêiner ao qual as mensagens são roteadas. O contêiner é um recurso sob a conta de armazenamento. Ele tem uma cláusula `dependsOn` para a conta de armazenamento, exigindo que a conta de armazenamento seja criada antes do contêiner.

Esta seção é assim:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Recursos: Fila e namespace do Barramento de Serviço

O segundo recurso criado é o namespace do Barramento de Serviço, juntamente com a fila do Barramento de Serviço para o qual as mensagens são roteadas. A SKU está definida como o padrão. A versão da API é recuperada das variáveis. Ela é também definida para ativar o namespace do Barramento de Serviço quando ele é implantado nesta seção (status: Ativo). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Esta seção cria a fila do Barramento de Serviço. Esta parte do script tem uma cláusula `dependsOn` que garante que o namespace foi criado antes da fila.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Recursos: Hub IOT e roteamento de mensagens

Agora que a conta de armazenamento e a fila do Barramento de Serviço foram criadas, você pode criar o Hub IoT que encaminha mensagens a elas. O modelo do RM usa cláusulas `dependsOn` para que não tente criar o hub antes da criação dos recursos do Barramento de Serviço e da conta de armazenamento. 

Aqui está a primeira parte da seção do Hub IoT. Esta parte do modelo configura as dependências e começa com as propriedades.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

A próxima seção é a seção para a configuração de roteamento de mensagens para o Hub IoT. Primeiro está a seção para os pontos de extremidade. Esta parte do modelo configura os pontos de extremidade de roteamentos para a fila do Barramento de Serviço e a conta de armazenamento, incluindo as cadeias de conexão.

Para criar a cadeia de conexão para a fila, você precisa de queueAuthorizationRulesResourcedId, que é obtido de forma embutida. Para criar a cadeia de conexão da conta de armazenamento, você pode recuperar a chave de armazenamento primária e, em seguida, usá-la no formato da cadeia de conexão.

É também na configuração de ponto de extremidade que você define o formato de blob como `AVRO` ou `JSON`.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Esta próxima seção é para rotas de mensagem para os pontos de extremidade. Há uma configuração para cada ponto de extremidade, portanto, há uma para a fila do Barramento de Serviço e outra para o contêiner da conta de armazenamento.

Lembre-se de que a condição de consulta para as mensagens que estão sendo roteadas para o armazenamento é `level="storage"` e a condição de consulta para as mensagens que estão sendo roteado para a fila do Barramento de Serviço é `level="critical"`.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Este json mostra o restante da seção do Hub IoT, que contém informações padrão e a SKU para o hub.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Recursos: Regras de autorização de fila do Barramento de Serviço

A regra de autorização de fila do Barramento de Serviço é usada para recuperar a cadeia de conexão para a fila do Barramento de Serviço. Ela usa uma cláusula `dependsOn` para assegurar que ela não seja criada antes do namespace do Barramento de Serviço e a fila do Barramento de Serviço.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Recursos: Grupo de consumidores

Nesta seção, você criará um grupo de consumidores para os dados do Hub IoT a serem usados pelo Azure Stream Analytics na segunda parte deste tutorial.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Recursos: outputs

Se você quiser enviar um valor de volta para o script de implantação para ser exibido, poderá usar uma seção de saída. Esta parte do modelo retorna a cadeia de conexão para a fila do Barramento de Serviço. Não é obrigatório retornar um valor, ele está incluído como um exemplo de como retornar resultados para o script de chamada.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Implantar o modelo do RM

Para implantar o modelo no Azure, carregue o modelo e o arquivo de parâmetros para o Azure Cloud Shell e, em seguida, execute um script para implantar o modelo. Abra o Azure Cloud Shell e entre. Este exemplo usa o PowerShell.

Para carregar os arquivos, selecione o ícone **Carregar/Baixar arquivos** na barra de menus e escolha Carregar.

![Captura de tela que realça o ícone Carregar/Baixar arquivos.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Use o Explorador de Arquivos que é exibido para localizar os arquivos no disco local e selecioná-los, então escolha **Abrir**.

Depois que os arquivos são carregados, uma caixa de diálogo de resultados mostra algo parecido com a imagem a seguir.

![Barra de menus do Cloud Shell com resultados de Upload/Download realçados](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Os arquivos são carregados para o compartilhamento usado por sua instância do Cloud Shell. 

Execute o script para executar a implantação. A última linha desse script recupera a variável configurada para ser retornada – a cadeia de conexão da fila do Barramento de Serviço.

O script define e usa estas variáveis:

**$RGName** é o nome de grupo de recursos ao qual implantar o modelo. Esse campo é criado antes de implantar o modelo.

**$location** é o local do Azure a ser usado para o modelo, como "Oeste dos EUA".

**deploymentname** é um nome que você atribui à implantação para recuperar o valor da variável de retorno.

Aqui está o script do PowerShell. Copie esse script do PowerShell, cole-o na janela do Cloud Shell e pressione Enter para executá-lo.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Se você tiver erros de script, edite o script localmente, carregue-o outra vez para o Cloud Shell e execute o script de novo. Depois que a execução do script for concluída com sucesso, prossiga para a etapa seguinte.

## <a name="create-simulated-device"></a>Criar dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Exibir o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem todos os recursos definidos e as rotas de mensagem configuradas, avance para o próximo tutorial para saber como processar e exibir as informações sobre as mensagens roteadas.

> [!div class="nextstepaction"]
> [Parte 2 – exibir os resultados de roteamento de mensagens](tutorial-routing-view-message-routing-results.md)
