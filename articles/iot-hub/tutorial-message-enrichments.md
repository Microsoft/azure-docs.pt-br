---
title: Tutorial – Usar os enriquecimentos de mensagens do Hub IoT do Azure
description: Tutorial que mostra como usar os enriquecimentos de mensagens do Hub IoT do Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0d6c90120d050b6896161f50332faf447c3ed67b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788845"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Tutorial: Usar os enriquecimentos de mensagens do Hub IoT do Azure

Os *enriquecimentos de mensagens* descrevem a capacidade do Hub IoT do Azure de *aplicar um carimbo de data/hora* às mensagens com informações adicionais antes que elas sejam enviadas para o ponto de extremidade designado. Um motivo para usar os enriquecimentos de mensagens é incluir dados que possam ser usados para simplificar o processamento downstream. Por exemplo, enriquecer mensagens de telemetria do dispositivo com uma marca de dispositivo gêmeo pode reduzir a carga em clientes para fazer chamadas à API do dispositivo gêmeo a fim de obter essas informações. Para obter mais informações, confira [Visão geral dos enriquecimentos de mensagens](iot-hub-message-enrichments-overview.md).

Neste tutorial, você verá duas maneiras de criar e configurar os recursos necessários para testar os enriquecimentos de mensagens para um hub IoT. Os recursos incluem uma conta de armazenamento com dois contêineres de armazenamento. Um contêiner armazena as mensagens enriquecidas e outro armazena as mensagens originais. Também é incluído um hub IoT para receber as mensagens e roteá-las para o contêiner de armazenamento apropriado, com base no fato de elas serem enriquecidas ou não.

* O primeiro método é usar a CLI do Azure para criar os recursos e configurar o roteamento de mensagens. Em seguida, você define os enriquecimentos manualmente usando o [portal do Azure](https://portal.azure.com).

* O segundo método é usar um modelo do Azure Resource Manager para criar os recursos *e* as configurações para o roteamento e os enriquecimentos de mensagens.

Depois que as configurações do roteamento e dos enriquecimentos de mensagens forem concluídas, você usará um aplicativo para enviar mensagens ao hub IoT. Em seguida, o hub roteia-as para os dois contêineres de armazenamento. Somente as mensagens enviadas ao ponto de extremidade do contêiner de armazenamento **enriquecido** são enriquecidas.

Estas são as tarefas que você realizará para concluir este tutorial:

**Usar os enriquecimentos de mensagens do Hub IoT**
> [!div class="checklist"]
> * Primeiro método: criar recursos e configurar o roteamento de mensagens usando a CLI do Azure. Configurar os enriquecimentos de mensagens manualmente usando o [portal do Azure](https://portal.azure.com).
> * Segundo método: criar recursos e configurar o roteamento e os enriquecimentos de mensagens usando um modelo do Resource Manager. 
> * Executar um aplicativo que simula um dispositivo IoT enviando mensagens para o hub.
> * Ver os resultados e verificar se os enriquecimentos de mensagens estão funcionando conforme o esperado.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ter uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instalar o [Visual Studio](https://www.visualstudio.com/).

- Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste tutorial usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recuperar o repositório de exemplos de C# da IoT

Baixe os [exemplos de C# da IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) do GitHub e descompacte-os. Esse repositório tem vários aplicativos, scripts e modelos do Resource Manager. Os itens a serem usados para este tutorial são os seguintes:

* Para o método manual, há um script da CLI que é usado para criar os recursos. Esse script está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Ele cria os recursos e configura o roteamento de mensagens. Depois de executar esse script, crie os enriquecimentos de mensagens manualmente usando o [portal do Azure](https://portal.azure.com).
* Para o método automatizado, há um modelo do Azure Resource Manager. O modelo está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Esse modelo cria os recursos, além de configurar o roteamento e os enriquecimentos de mensagens.
* O terceiro aplicativo usado é o aplicativo Simulação de Dispositivo, que você usa para enviar mensagens ao hub IoT e testar os enriquecimentos de mensagens.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Configuração manual usando a CLI do Azure

Além de criar os recursos necessários, o script da CLI do Azure também configura as duas rotas para os pontos de extremidade que são contêineres de armazenamento separados. Para obter mais informações sobre como configurar o roteamento de mensagens, confira o [Tutorial de roteamento](tutorial-routing.md). Depois que os recursos forem configurados, use o [portal do Azure](https://portal.azure.com) para configurar os enriquecimentos de mensagens para cada ponto de extremidade. Depois, prossiga para a etapa do teste.

> [!NOTE]
> Todas as mensagens são roteadas para os dois pontos de extremidade, mas somente as mensagens enviadas para o ponto de extremidade com os enriquecimentos de mensagens configurados serão enriquecidas.
>

Você pode usar o script a seguir ou abrir o script na pasta /resources do repositório baixado. O script executa as seguintes etapas:

* Crie um Hub IoT.
* Criar uma conta de armazenamento.
* Criar dois contêineres na conta de armazenamento. Um contêiner destina-se às mensagens enriquecidas e outro destina-se às mensagens que não são enriquecidas.
* Configurar o roteamento para as duas contas de armazenamento diferentes:
    * Criar um ponto de extremidade para cada contêiner da conta de armazenamento.
    * Criar uma rota para cada um dos pontos de extremidade do contêiner da conta de armazenamento.

Há vários nomes de recursos que precisam ser globais exclusivos, como o nome do hub IoT e o nome da conta de armazenamento. Para facilitar a execução do script, esses nomes de recursos são acrescentados com um valor alfanumérico aleatório chamado *randomValue*. O valor aleatório é gerado uma vez na parte superior do script. Ele é acrescentado aos nomes de recursos conforme necessário em todo o script. Se você não quiser que o valor seja aleatório, defina-o como uma cadeia de caracteres vazia ou um valor específico.

Se você ainda não tiver feito isso, abra uma [janela do Azure Cloud Shell](https://shell.azure.com) e verifique se ela está definida como Bash. Abra o script no repositório descompactado, selecione CTRL + A para selecioná-lo todo e selecione CTRL + C para copiá-lo. Como alternativa, você pode copiar o script da CLI a seguir ou abri-lo diretamente no Cloud Shell. Cole o script na janela do Cloud Shell clicando com o botão direito do mouse na linha de comando e escolhendo **Colar**. O script executa uma instrução de cada vez. Depois que o script parar de ser executado, selecione **ENTER** para que ele execute o último comando. O bloco de código a seguir mostra o script que é usado, com os comentários que explicam o que ele está fazendo.

Estes são os recursos criados pelo script. *Enriquecido* significa que o recurso se destina às mensagens com enriquecimentos. *Original* significa que o recurso se destina às mensagens que não são enriquecidas.

| Nome | Valor |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome do contêiner | original  |
| nome do contêiner | enriquecido  |
| nome do dispositivo IoT | Contoso-Test-Device |
| Nome do Hub IoT | ContosoTestHubMsgEn |
| nome da conta de armazenamento | contosostorage |
| nome do ponto de extremidade 1 | ContosoStorageEndpointOriginal |
| nome do ponto de extremidade 2 | ContosoStorageEndpointEnriched|
| nome da rota 1 | ContosoStorageRouteOriginal |
| nome da rota 2 | ContosoStorageRouteEnriched |

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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Neste ponto, os recursos estão todos configurados e o roteamento de mensagens está configurado. Veja a configuração do roteamento de mensagens no portal e configure os enriquecimentos de mensagens para as mensagens destinadas ao contêiner de armazenamento **enriquecido**.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configurar manualmente os enriquecimentos de mensagens usando o portal do Azure

1. Acesse o hub IoT selecionando **Grupos de recursos**. Depois, escolha o grupo de recursos configurado para este tutorial (**ContosoResourcesMsgEn**). Localize o hub IoT na lista e selecione-o. Selecione **Roteamento de mensagens** para o hub IoT.

   ![Selecionar o roteamento de mensagens](./media/tutorial-message-enrichments/select-iot-hub.png)

   O painel de roteamento de mensagens tem três guias rotuladas **Rotas**, **Pontos de extremidade personalizados** e **Enriquecer mensagens**. Navegue pelas duas primeiras guias para ver a configuração definida pelo script. Use a terceira guia para adicionar enriquecimentos de mensagens. Vamos enriquecer as mensagens destinadas ao ponto de extremidade do contêiner de armazenamento chamado **enriquecido**. Preencha o nome e o valor e selecione o ponto de extremidade **ContosoStorageEndpointEnriched** na lista suspensa. Veja um exemplo de como configurar um enriquecimento que adiciona o nome do hub IoT à mensagem:

   ![Adicionar o primeiro enriquecimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adicione estes valores à lista para o ponto de extremidade ContosoStorageEndpointEnriched.

   | Chave | Valor | Ponto de extremidade (lista suspensa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se o dispositivo não tiver um gêmeo, o valor que você colocar aqui terá um carimbo de data/hora como uma cadeia de caracteres para o valor nos enriquecimentos de mensagens. Para ver as informações do dispositivo gêmeo, acesse o seu hub no portal e selecione **Dispositivos IoT**. Escolha seu dispositivo e selecione **Dispositivo gêmeo** na parte superior da página.
   >
   > Você pode editar as informações do gêmeo para adicionar marcas, como localização, e defini-la como um valor específico. Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](iot-hub-devguide-device-twins.md).

3. Quando você terminar, o painel será semelhante a esta imagem:

   ![Tabela com todos os enriquecimentos adicionados](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Escolha **Aplicar** para salvar as alterações. Vá para a seção [Testar enriquecimentos de mensagens](#test-message-enrichments).

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Criação e configuração por meio de um modelo do Resource Manager
Você pode usar um modelo do Resource Manager para criar e configurar os recursos, bem como o roteamento e os enriquecimentos de mensagens.

1. Entre no portal do Azure. Selecione **+ Criar um recurso** para abrir uma caixa de pesquisa. Insira *implantação de modelo* e pesquise o termo. No painel de resultados, selecione **Implantação de modelo (implantação por meio de um modelo personalizado)** .

   ![Implantação de modelo no portal do Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecione **Criar** no painel **Implantação de modelo**.

1. No painel **Implantação personalizada**, escolha **Criar seu modelo no editor**.

1. No painel **Editar modelo**, escolha **Carregar arquivo**. O Windows Explorer será exibido. Localize o arquivo **template_messageenrichments.json** no arquivo de repositório descompactado em **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Selecionar modelo no computador local](./media/tutorial-message-enrichments/template-select.png)

1. Escolha **Abrir** para carregar o arquivo de modelo do computador local. Ele será carregado e exibido no painel de edição.

   Esse modelo é configurado para usar um nome de hub IoT e um nome de conta de armazenamento globais exclusivos adicionando um valor aleatório ao final dos nomes padrão, de modo que você possa usar o modelo sem fazer nenhuma alteração nele.

   Estes são os recursos criados com o carregamento do modelo. **Enriquecido** significa que o recurso se destina às mensagens com enriquecimentos. **Original** significa que o recurso se destina às mensagens que não são enriquecidas. Estes são os mesmos valores usados no script da CLI do Azure.

   | Nome | Valor |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome do contêiner | original  |
   | nome do contêiner | enriquecido  |
   | nome do dispositivo IoT | Contoso-Test-Device |
   | Nome do Hub IoT | ContosoTestHubMsgEn |
   | nome da conta de armazenamento | contosostorage |
   | nome do ponto de extremidade 1 | ContosoStorageEndpointOriginal |
   | nome do ponto de extremidade 2 | ContosoStorageEndpointEnriched|
   | nome da rota 1 | ContosoStorageRouteOriginal |
   | nome da rota 2 | ContosoStorageRouteEnriched |

1. Clique em **Salvar**. O painel **Implantação personalizada** será exibido e mostrará todos os parâmetros usados pelo modelo. O único campo que você precisará definir é **Grupo de recursos**. Crie ou selecione um na lista suspensa.

   Esta é a metade superior do painel **Implantação personalizada**. Você pode ver o local em que preencherá o grupo de recursos.

   ![Metade superior do painel Implantação personalizada](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Esta é a metade inferior do painel **Implantação personalizada**. Você pode ver o restante dos parâmetros, bem como os termos e as condições. 

   ![Metade inferior do painel Implantação personalizada](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Marque a caixa de seleção para concordar com os termos e as condições. Em seguida, selecione **Comprar** para continuar com a implantação de modelo.

1. Aguarde até que o modelo seja totalmente implantado. Selecione o ícone de sino na parte superior da tela para verificar o progresso. Quando terminar, prossiga para a seção [Testar enriquecimentos de mensagens](#test-message-enrichments).

## <a name="test-message-enrichments"></a>Testar enriquecimentos de mensagens

Para ver os enriquecimentos de mensagens, selecione **Grupos de recursos**. Depois, escolha o grupo de recursos que você está usando para este tutorial. Selecione o hub IoT na lista de recursos e acesse **Mensagens**. A configuração de roteamento de mensagens e os enriquecimentos configurados serão exibidos.

Agora que os enriquecimentos de mensagens estão configurados para o ponto de extremidade, execute o aplicativo Dispositivo Simulado para enviar mensagens para o hub IoT. O hub foi definido com as configurações que realizam as seguintes tarefas:

* As mensagens roteadas para o ponto de extremidade de armazenamento ContosoStorageEndpointOriginal não serão enriquecidas e serão armazenadas no contêiner de armazenamento `original`.

* As mensagens roteadas para o ponto de extremidade de armazenamento ContosoStorageEndpointEnriched serão enriquecidas e armazenadas no contêiner de armazenamento `enriched`.

O aplicativo Dispositivo Simulado é um dos aplicativos do download descompactado. Ele envia mensagens para cada um dos diferentes métodos de roteamento de mensagens no [Tutorial de roteamento](tutorial-routing.md), que inclui o Armazenamento do Azure.

Clique duas vezes no arquivo de solução **IoT_SimulatedDevice.sln** para abrir o código no Visual Studio e abra **Program.cs**. Substitua o nome do hub IoT pelo marcador `{your hub name}`. O formato do nome do host do hub IoT é **{nome do hub}.azure-devices.net**. Para este tutorial, o nome do host do hub é ContosoTestHubMsgEn.azure-devices.net. Em seguida, substitua a chave do dispositivo que você salvou anteriormente quando executou o script para criar os recursos para o marcador `{your device key}`.

Se não tiver a chave do dispositivo, recupere-a no portal. Depois de se conectar, acesse **Grupos de recursos**, selecione o grupo de recursos e escolha o hub IoT. Procure seu dispositivo de teste em **Dispositivos IoT** e selecione-o. Escolha o ícone de cópia ao lado da **Chave primária** para copiá-la para a área de transferência.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Executar e testar

Execute o aplicativo de console por alguns minutos. As mensagens que estão sendo enviadas são exibidas na tela do console do aplicativo.

Esse aplicativo envia uma nova mensagem de dispositivo para a nuvem ao Hub IoT a cada segundo. A mensagem contém um objeto serializado para JSON com a ID do dispositivo, temperatura, umidade e nível de mensagem, cujo padrão é `normal`. Ele atribui aleatoriamente um nível `critical` ou `storage`, o que faz com que a mensagem seja roteada para a conta de armazenamento ou para o ponto de extremidade padrão. As mensagens enviadas para o contêiner **enriquecido** na conta de armazenamento serão enriquecidas.

Depois que várias mensagens de armazenamento forem enviadas, veja os dados.

1. Selecione **Grupos de recursos**. Localize seu grupo de recursos, **ContosoResourcesMsgEn**, e selecione-o.

2. Selecione sua conta de armazenamento, que é **contosostorage**. Em seguida, escolha **Gerenciador de Armazenamento (versão prévia)** no painel esquerdo.

   ![Selecionar o Gerenciador de Armazenamento](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Escolha **CONTÊINERES DE BLOB** para ver os dois contêineres que podem ser usados.

   ![Ver os contêineres na conta de armazenamento](./media/tutorial-message-enrichments/show-blob-containers.png)

As mensagens do contêiner chamadas **enriquecidas** têm os enriquecimentos de mensagens incluídos nas mensagens. As mensagens do contêiner chamadas **originais** têm as mensagens brutas sem enriquecimentos. Faça uma busca detalhada em um dos contêineres até chegar à parte inferior e abra o arquivo de mensagem mais recente. Em seguida, faça o mesmo para o outro contêiner para verificar se não há enriquecimentos adicionados às mensagens desse contêiner.

Ao examinar as mensagens que foram enriquecidas, você deverá ver "meu Hub IoT" com o nome do hub e a localização, bem como a ID do cliente, desta forma:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Esta é uma mensagem não enriquecida. Observe que "meu Hub IoT", "devicelocation" e "customerID" não são mostrados aqui porque esses campos são adicionados pelos enriquecimentos. Esse ponto de extremidade não tem enriquecimentos.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos que você criou neste tutorial, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ela remove o hub IoT, a conta de armazenamento e o próprio grupo de recursos.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usar a CLI do Azure para limpar os recursos

Para remover o grupo de recursos, use o comando [excluir grupo az](/cli/azure/group#az_group_delete). Lembre-se de que `$resourceGroup` foi definido como **ContosoResourcesMsgEn** no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e testou a adição de enriquecimentos de mensagens às mensagens do Hub IoT usando as seguintes etapas:

**Usar os enriquecimentos de mensagens do Hub IoT**

> [!div class="checklist"]
> * Primeiro método: criar recursos e configurar o roteamento de mensagens usando a CLI do Azure. Configurar os enriquecimentos de mensagens manualmente usando o [portal do Azure](https://portal.azure.com).
> * Segundo método: criar recursos e configurar o roteamento e os enriquecimentos de mensagens usando um modelo do Azure Resource Manager.
> * Executar um aplicativo que simula um dispositivo IoT enviando mensagens para o hub.
> * Ver os resultados e verificar se os enriquecimentos de mensagens estão funcionando conforme o esperado.

Para obter mais informações sobre os enriquecimentos de mensagens, confira [Visão geral dos enriquecimentos de mensagens](iot-hub-message-enrichments-overview.md).

Para obter mais informações sobre o roteamento de mensagens, confira estes artigos:

> [!div class="nextstepaction"]
> [Usar o roteamento de mensagens do Hub IoT para enviar mensagens de dispositivo para nuvem para diferentes pontos de extremidade](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Tutorial: Roteamento do Hub IoT](tutorial-routing.md)