---
title: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure usando a CLI do Azure
description: Tutorial – Configurar o roteamento de mensagens para o Hub IoT do Azure usando a CLI e o portal do Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: d7a10aac82286616273f7283bc3fba74f2c58f35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625208"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Tutorial: Usar a CLI do Azure e o portal do Azure para configurar o roteamento de mensagens do Hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Usar a CLI do Azure para criar os recursos de base

Este tutorial usa a CLI do Azure para criar os recursos de base, então usa o [portal do Azure](https://portal.azure.com) para mostrar como configurar o roteamento de mensagens e configurar o dispositivo virtual para teste.

Copie e cole o script abaixo no Cloud Shell e pressione Enter. Ele executa o script uma linha por vez. Isso criará os recursos de base para este tutorial, incluindo a conta de armazenamento, o Hub IoT, o Namespace de Barramento de Serviço e a fila do Barramento de Serviço.

Há vários nomes de recurso que devem ser globalmente exclusivos, como o nome do Hub IoT e o nome da conta de armazenamento. Para facilitar essa tarefa, esses nomes de recurso são acrescentados com um valor alfanumérico aleatório chamado *randomValue*. O randomValue é gerado uma vez na parte superior do script e acrescentado aos nomes de recursos conforme necessário em todo o script. Se você não quiser que ele seja aleatório, defina uma cadeia de caracteres vazia ou como um valor específico.

> [!TIP]
> Uma dica sobre depuração: esse script usa o símbolo de continuação (a barra invertida `\`) para tornar o script mais legível. Se você tiver um problema ao executar o script, verifique se sua sessão do Cloud Shell está executando `bash` e se não há espaços após as barras invertidas.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Agora que os recursos básicos foram configurados, você pode configurar o roteamento de mensagens no [portal do Azure](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Configurar roteamento de mensagens

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Rotear para uma conta de armazenamento

Agora, configure o roteamento para a conta de armazenamento. Acesse o painel Roteamento de Mensagens e adicione uma rota. Ao adicionar a rota, defina um novo ponto de extremidade para ela. Depois que esse roteamento estiver configurado, as mensagens em que a propriedade **nível** estiver definida como **armazenamento** serão gravadas em uma conta de armazenamento automaticamente. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. No [portal do Azure](https://portal.azure.com), selecione **Grupos de Recursos** e selecione seu grupo de recursos. Este tutorial usa **ContosoResources**.

2. Selecione o hub IoT na lista de recursos. Este tutorial usa **ContosoTestHub**.

3. Selecione **Roteamento de Mensagens**. No painel **Roteamento de Mensagens**, selecione +**Adicionar**. No painel **Adicionar uma Rota**, selecione +**Adicionar ponto de extremidade** ao lado do campo Ponto de Extremidade para mostrar os pontos de extremidade compatíveis, conforme exibido na seguinte imagem:

   ![Começar a adicionar um ponto de extremidade a uma rota](./media/tutorial-routing/message-routing-add-a-route-with-storage-endpoint-ver2.png)

4. Selecione **Armazenamento**. Você verá o painel **Adicionar um ponto de extremidade de armazenamento**.

   ![Adicionando um ponto de extremidade](./media/tutorial-routing/message-routing-add-storage-endpoint-ver2.png)

5. Insira um nome para o ponto de extremidade. Este tutorial usa **ContosoStorageEndpoint**.

6. Selecione **Escolher um contêiner**. Você será direcionado a uma lista com as suas contas de armazenamento. Selecione aquela que você configurou nas etapas de preparação. Este tutorial usa **contosostorage**. É mostrada uma lista de contêineres nessa conta de armazenamento. **Selecione** o contêiner que você configurou nas etapas de preparação. Este tutorial usa **contosoresults**. Você retorna ao painel **Adicionar um ponto de extremidade de armazenamento** e vê as seleções feitas.

7. Defina a codificação como AVRO ou JSON. Para fins deste tutorial, use os padrões para o restante dos campos. Este campo ficará esmaecido se a região selecionada não for compatível com a codificação JSON.

   > [!NOTE]
   > Você pode definir o formato do nome de blob usando o **formato de nome de arquivo de Blob**. O padrão é `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. O formato deve conter {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} e {mm} em qualquer ordem.
   >
   > Por exemplo, usando o formato de nome de arquivo de blob padrão, se o nome do hub for ContosoTestHub e a data/hora for 30 de outubro de 2018, às 10:56, o nome do blob será assim: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Os blobs são gravados no formato Avro.
   >

8. Selecione **Criar** para criar o ponto de extremidade de armazenamento e adicioná-lo à rota. Você retornará ao painel **Adicionar uma rota**.

9. Agora, preencha o restante das informações da consulta de roteamento. Essa consulta especifica os critérios para enviar mensagens ao contêiner de armazenamento que você acabou de adicionar como um ponto de extremidade. Preencha os campos na tela.

   **Name**: insira um nome para a consulta de roteamento. Este tutorial usa **ContosoStorageRoute**.

   **Ponto de extremidade**: mostra o ponto de extremidade que você acabou de configurar.

   **Fonte de dados**: selecione **Mensagens de Telemetria do Dispositivo** na lista suspensa.

   **Habilitar rota**: Verifique se esse campo está definido como `enabled`.
   
   **Consulta de roteamento**: insira `level="storage"` como a cadeia de caracteres de consulta.

   ![Criando uma consulta de roteamento para a conta de armazenamento](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Clique em **Salvar**. Ao terminar, você retornará ao painel Roteamento de Mensagens, que exibe sua nova consulta de roteamento para o armazenamento. Feche o painel de Rotas, que retorna para a página de Grupo de Recursos.

### <a name="route-to-a-service-bus-queue"></a>Rotear para uma fila do Barramento de Serviço

Agora, configure o roteamento para a fila do Barramento de Serviço. Acesse o painel Roteamento de Mensagens e adicione uma rota. Ao adicionar a rota, defina um novo ponto de extremidade para ela. Depois que essa rota estiver configurada, a mensagem em que a propriedade **nível** está definida como **crítica** será gravada na fila do Barramento de Serviço, que dispara um Aplicativo Lógico que, por sua vez, envia um email com as informações.

1. Na página do Grupo de recursos, selecione seu hub IoT e, em seguida, selecione **Roteamento de Mensagens**.

2. No painel **Roteamento de Mensagens**, selecione +**Adicionar**.

3. No painel **Adicionar uma Rota**, selecione +**Adicionar** ao lado do campo Ponto de Extremidade. Selecione **Fila do Barramento de Serviço**. O painel **Adicionar Ponto de Extremidade do Barramento de Serviço** é exibido.

   ![Adicionando um ponto de extremidade do barramento de serviço](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Preencha os campos:

   **Nome do Ponto de Extremidade**: Insira um nome para o ponto de extremidade. Este tutorial usa **ContosoSBQueueEndpoint**.
   
   **Namespace do Barramento de Serviço**: Use a lista suspensa para selecionar o namespace do barramento de serviço configurado nas etapas de preparação. Este tutorial usa **ContosoSBNamespace**.

   **Fila do Barramento de Serviço**: Use a lista suspensa para selecionar a fila do Barramento de Serviço. Este tutorial usa **contososbqueue**.

5. Selecione **Criar** para adicionar o ponto de extremidade da fila do Barramento de Serviço. Você retornará ao painel **Adicionar uma rota**.

6. Agora, preencha o restante das informações da consulta de roteamento. Essa consulta especifica os critérios para enviar mensagens à fila do Barramento de Serviço que você acabou de adicionar como um ponto de extremidade. Preencha os campos na tela. 

   **Name**: insira um nome para a consulta de roteamento. Este tutorial usa **ContosoSBQueueRoute**. 

   **Ponto de extremidade**: mostra o ponto de extremidade que você acabou de configurar.

   **Fonte de dados**: selecione **Mensagens de Telemetria do Dispositivo** na lista suspensa.

   **Consulta de roteamento**: insira `level="critical"` como a cadeia de caracteres de consulta. 

   ![Criar uma consulta de roteamento para a fila do Barramento de Serviço](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Clique em **Salvar**. Ao retornar ao painel Rotas, você verá suas duas novas rotas, como exibido aqui.

   ![As rotas que você acabou de configurar](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Veja os pontos de extremidade personalizados que você configurou selecionando a guia **Pontos de Extremidade Personalizados**.

   ![O ponto de extremidade personalizado que você acabou de configurar](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Feche o painel Roteamento de Mensagens e você retornará ao painel Grupo de recursos.

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem os recursos definidos e as rotas de mensagem configuradas, avance para o próximo tutorial para aprender a enviar mensagens ao hub IoT e vê-las serem roteadas para diferentes destinos. 

> [!div class="nextstepaction"]
> [Parte 2 – exibir os resultados de roteamento de mensagens](tutorial-routing-view-message-routing-results.md)