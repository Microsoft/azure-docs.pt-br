---
title: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure usando a CLI do Azure
description: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure usando a CLI do Azure. Dependendo das propriedades da mensagem, encaminhe para uma conta de armazenamento ou para uma fila do Barramento de Serviço.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cf994dfe3d53232ab1e2374fda620dc768127097
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788881"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Tutorial: Usar a CLI do Azure para configurar o roteamento de mensagens do Hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Baixe o script (opcional)

Para a segunda parte deste tutorial, você pode baixar e executar um aplicativo do Visual Studio para enviar mensagens ao Hub IoT. Há uma pasta no download que contém o arquivo de parâmetros e o modelo do Azure Resource Manager, bem como os scripts do PowerShell e CLI do Azure.

Se você quiser exibir o script concluído, baixe os [Exemplos do C# do Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Descompacte o arquivo master.zip. O script da CLI do Azure está em /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ as **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Usar a CLI do Azure para criar seus recursos

Copie e cole o script abaixo no Cloud Shell e pressione Enter. Ele executa o script uma linha por vez. Esta primeira seção do script criará os recursos de base para este tutorial, incluindo a conta de armazenamento, o Hub IoT, o Namespace de Barramento de Serviço e a fila do Barramento de Serviço. Ao percorrer o restante do tutorial, copie cada bloco de script e cole-o no Cloud Shell para executá-lo.

> [!TIP]
> Uma dica sobre depuração: esse script usa o símbolo de continuação (a barra invertida `\`) para tornar o script mais legível. Se você tiver um problema ao executar o script, verifique se sua sessão do Cloud Shell está executando `bash` e se não há espaços após as barras invertidas.
> 

Há vários nomes de recurso que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para facilitar essa tarefa, esses nomes de recurso são acrescentados com um valor alfanumérico aleatório chamado *randomValue*. O randomValue é gerado uma vez na parte superior do script e acrescentado aos nomes de recursos conforme necessário em todo o script. Se você não quiser que ele seja aleatório, defina uma cadeia de caracteres vazia ou como um valor específico. 

> [!IMPORTANT]
> As variáveis definidas no script inicial também são usadas pelo script de roteamento, portanto, execute todos os scripts na mesma sessão do Cloud Shell. Se você abrir uma nova sessão para executar o script para configurar o roteamento, diversas variáveis estarão sem valores.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Agora que os recursos básicos foram configurados, você pode configurar o roteamento de mensagens.

## <a name="set-up-message-routing"></a>Configurar roteamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Para criar um ponto de extremidade de roteamento, use [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create). Para criar a rota de mensagem para o ponto de extremidade, use [az iot hub route create](/cli/azure/iot/hub/route#az_iot_hub_route_create).

### <a name="route-to-a-storage-account"></a>Rotear para uma conta de armazenamento

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Primeiro, configure o ponto de extremidade para a conta de armazenamento e configure a rota. 

Essas são as variáveis usadas pelo script que devem ser definidas dentro de sua sessão do Cloud Shell:

**storageConnectionString**: esse valor é recuperado da conta de armazenamento configurada no script anterior. Ele é usado pelo roteamento de mensagens para acessar a conta de armazenamento.

  **resourceGroup**: há duas ocorrências do grupo de recursos – defina-as para seu grupo de recursos.

**endpoint subscriptionID**: esse campo é definido como a subscriptionID do Azure para o ponto de extremidade. 

**endpointType**: esse campo é o tipo de ponto de extremidade. Esse valor deve ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue` ou `servicebustopic`. Para suas finalidades aqui, defina-o como `azurestoragecontainer`.

**iotHubName**: esse campo é o nome do hub que fará o roteamento.

**containerName**: esse campo é o nome do contêiner na conta de armazenamento na qual os dados serão gravados.

**encoding**: esse campo será `avro` ou `json`. Isso denota o formato dos dados armazenados.

**routeName**: esse campo é o nome da rota para que você está configurando. 

**endpointName**: esse campo é o nome que identifica o ponto de extremidade. 

**enabled**: Esse campo assume `true` como padrão, indicando que a rota da mensagem deve ser habilitada depois de ser criada.

**condition**: esse campo é a consulta usada para filtrar as mensagens enviadas para esse ponto de extremidade. A condição de consulta para as mensagens que estão sendo roteadas para o armazenamento é `level="storage"`.

Copie esse script e cole-o na janela do Cloud Shell e execute-o.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

A próxima etapa é criar o ponto de extremidade de roteamento para a conta de armazenamento. Você também pode especificar o contêiner no qual os resultados serão armazenados. O contêiner foi criado anteriormente quando a conta de armazenamento foi criada.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Em seguida, crie a rota para o ponto de extremidade de armazenamento. A rota de mensagem designa para que local enviar as mensagens que atendem à especificação de consulta. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Rotear para uma fila do Barramento de Serviço

Agora, configure o roteamento para a fila do Barramento de Serviço. Para recuperar a cadeia de conexão para a fila do Barramento de Serviço, você deve criar uma regra de autorização com os direitos corretos definidos. O script a seguir cria uma regra de autorização para a fila do Barramento de Serviço chamada `sbauthrule` e define os direitos para `Listen Manage Send`. Depois que essa regra de autorização tiver sido definida, você poderá usá-la para recuperar a cadeia de conexão para a fila.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Agora, use a regra de autorização para recuperar a cadeia de conexão para a fila do Barramento de Serviço.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Agora configure o ponto de extremidade de roteamento e a rota de mensagem para a fila do Barramento de Serviço. Essas são as variáveis usadas pelo script que devem ser definidas dentro de sua sessão do Cloud Shell:

**endpointName**: esse campo é o nome que identifica o ponto de extremidade. 

**endpointType**: esse campo é o tipo de ponto de extremidade. Esse valor deve ser definido como `azurestoragecontainer`, `eventhub`, `servicebusqueue` ou `servicebustopic`. Para suas finalidades aqui, defina-o como `servicebusqueue`.

**routeName**: esse campo é o nome da rota para que você está configurando. 

**condition**: esse campo é a consulta usada para filtrar as mensagens enviadas para esse ponto de extremidade. A condição de consulta para as mensagens que está sendo roteadas para a fila do Barramento de Serviço é `level="critical"`.

Aqui está a CLI do Azure para o ponto de extremidade de roteamento e a rota de mensagem para a fila do Barramento de Serviço.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Exibir o roteamento de mensagens no portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem os recursos definidos e as rotas de mensagem configuradas, avance para o próximo tutorial para aprender a enviar mensagens ao hub IoT e vê-las serem roteadas para diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2 – exibir os resultados de roteamento de mensagens](tutorial-routing-view-message-routing-results.md)
