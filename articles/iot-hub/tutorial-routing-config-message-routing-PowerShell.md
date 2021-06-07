---
title: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure com o Azure PowerShell
description: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure usando o Azure PowerShell. Dependendo das propriedades da mensagem, encaminhe para uma conta de armazenamento ou para uma fila do Barramento de Serviço.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be1560bcc03ec7a26f4bc374392c746243cd731a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624065"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Tutorial: Usar o Azure PowerShell para configurar o roteamento de mensagens do Hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Baixe o script (opcional)

Para a segunda parte deste tutorial, você pode baixar e executar um aplicativo do Visual Studio para enviar mensagens ao Hub IoT. Há uma pasta no download que contém o arquivo de parâmetros e o modelo do Azure Resource Manager, bem como os scripts do PowerShell e CLI do Azure. 

Se você quiser exibir o script concluído, baixe os [Exemplos do C# do Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Descompacte o arquivo master.zip. O script da CLI do Azure está em /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ as **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Criar seus recursos

Comece criando os recursos com o PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Use o PowerShell para criar seus recursos de base

Copie e cole o script abaixo no Cloud Shell e pressione Enter. Ele executa o script uma linha por vez. Esta primeira seção do script criará os recursos de base para este tutorial, incluindo a conta de armazenamento, o Hub IoT, o Namespace de Barramento de Serviço e a fila do Barramento de Serviço. Ao percorrer o tutorial, copie cada bloco de script e cole-o no Cloud Shell para executá-lo.

Há vários nomes de recurso que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para facilitar essa tarefa, esses nomes de recurso são acrescentados com um valor alfanumérico aleatório chamado *randomValue*. O randomValue é gerado uma vez na parte superior do script e acrescentado aos nomes de recursos conforme necessário em todo o script. Se você não quiser que ele seja aleatório, defina uma cadeia de caracteres vazia ou como um valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são usadas pelo script de roteamento, portanto, execute todos os scripts na mesma sessão do Cloud Shell. Se você abrir uma nova sessão para executar o script para configurar o roteamento, diversas variáveis estarão sem valores. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Agora que os recursos básicos foram configurados, você pode configurar o roteamento de mensagens.

## <a name="set-up-message-routing"></a>Configurar roteamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto de extremidade de roteamento, use [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Para criar a rota de mensagens para o ponto de extremidade, use [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Rotear para uma conta de armazenamento 

Primeiro, configure o ponto de extremidade para a conta de armazenamento e crie a rota de mensagem.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Essas são as variáveis usadas pelo script que devem ser definidas dentro de sua sessão do Cloud Shell:

**resourceGroup**: há duas ocorrências desse campo – defina ambas para seu grupo de recursos.

**nome**: esse campo é o nome do Hub IoT ao qual o roteamento será aplicado.

**endpointName**: esse campo é o nome que identifica o ponto de extremidade. 

**endpointType**: esse campo é o tipo de ponto de extremidade. Esse valor deve ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue` ou `servicebustopic`. Para suas finalidades aqui, defina-o como `azurestoragecontainer`.

**subscriptionID**: esse campo é definido como a subscriptionID para sua conta do Azure.

**storageConnectionString**: esse valor é recuperado da conta de armazenamento configurada no script anterior. Ele é usado pelo roteamento para acessar a conta de armazenamento.

**containerName**: esse campo é o nome do contêiner na conta de armazenamento na qual os dados serão gravados.

**Encoding**: defina esse campo como `AVRO` ou `JSON`. Designa o formato dos dados armazenados. O padrão é AVRO.

**routeName**: esse campo é o nome da rota para que você está configurando. 

**condition**: esse campo é a consulta usada para filtrar as mensagens enviadas para esse ponto de extremidade. A condição de consulta para as mensagens que estão sendo roteadas para o armazenamento é `level="storage"`.

**enabled**: Esse campo assume `true` como padrão, indicando que a rota da mensagem deve ser habilitada depois de ser criada.

Copie esse script e cole-o na janela do Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

A próxima etapa é criar o ponto de extremidade de roteamento para a conta de armazenamento. Você também pode especificar o contêiner no qual os resultados serão armazenados. O contêiner foi criado quando a conta de armazenamento foi criada.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Em seguida, crie a rota de mensagem para o ponto de extremidade de armazenamento. A rota de mensagem designa para que local enviar as mensagens que atendem à especificação de consulta.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Rotear para uma fila do Barramento de Serviço

Agora, configure o roteamento para a fila do Barramento de Serviço. Para recuperar a cadeia de conexão para a fila do Barramento de Serviço, você deve criar uma regra de autorização com os direitos corretos definidos. O script a seguir cria uma regra de autorização para a fila do Barramento de Serviço chamada `sbauthrule` e define os direitos para `Listen Manage Send`. Depois que essa regra de autorização tiver sido configurada, você poderá usá-la para recuperar a cadeia de conexão para a fila.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Agora use a regra de autorização para recuperar a chave de fila do Barramento de Serviço. Essa regra de autorização será usada para recuperar a cadeia de conexão posteriormente no script.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Agora configure o ponto de extremidade de roteamento e a rota de mensagem para a fila do Barramento de Serviço. Essas são as variáveis usadas pelo script que devem ser definidas dentro de sua sessão do Cloud Shell:

**endpointName**: esse campo é o nome que identifica o ponto de extremidade. 

**endpointType**: esse campo é o tipo de ponto de extremidade. Esse valor deve ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue` ou `servicebustopic`. Para suas finalidades aqui, defina-o como `servicebusqueue`.

**routeName**: esse campo é o nome da rota para que você está configurando. 

**condition**: esse campo é a consulta usada para filtrar as mensagens enviadas para esse ponto de extremidade. A condição de consulta para as mensagens que está sendo roteadas para a fila do Barramento de Serviço é `level="critical"`.

Aqui está o Azure PowerShell para o roteamento de mensagens para a fila do Barramento de Serviço.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Exibir o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem os recursos definidos e as rotas de mensagem configuradas, avance para o próximo tutorial para aprender a enviar mensagens ao hub IoT e vê-las serem roteadas para diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2 – exibir os resultados de roteamento de mensagens](tutorial-routing-view-message-routing-results.md)
