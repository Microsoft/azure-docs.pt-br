---
title: Gerenciar dispositivos automaticamente usando o serviço de provisionamento de dispositivos
titleSuffix: Azure Digital Twins
description: Veja como configurar um processo automatizado para provisionar e desativar dispositivos IoT no Azure digital gêmeos usando o DPS (serviço de provisionamento de dispositivos).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a571d92dd9663c7d2d0a576b59e5cd2b3352cb76
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950938"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Gerenciar dispositivos automaticamente no gêmeos digital do Azure usando o DPS (serviço de provisionamento de dispositivos)

Neste artigo, você aprenderá a integrar o Azure digital gêmeos ao [DPS (serviço de provisionamento de dispositivos)](../iot-dps/about-iot-dps.md).

A solução descrita neste artigo permitirá que você automatize o processo para **_provisionar_** e **_desativar_** dispositivos do Hub IOT no gêmeos digital do Azure usando o serviço de provisionamento de dispositivos. 

Para obter mais informações sobre os estágios de _provisionamento_ e _desativação_ , e entender melhor o conjunto de estágios gerais de gerenciamento de dispositivos que são comuns a todos os projetos de IOT corporativos, consulte a [seção *ciclo de vida do dispositivo*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) da documentação de gerenciamento de dispositivos do Hub IOT.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder configurar o provisionamento, você precisará configurar o seguinte:
* uma **instância do gêmeos digital do Azure**. Siga as instruções em [*como: configurar uma instância e autenticação*](how-to-set-up-instance-portal.md) para criar uma instância de gêmeos digital do Azure. Reúna o nome do **_host_** da instância no portal do Azure ([instruções](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* um **Hub IOT**. Para obter instruções, consulte a seção *criar um hub IOT* deste [início rápido do Hub IOT](../iot-hub/quickstart-send-telemetry-cli.md).
* uma [**função do Azure**](../azure-functions/functions-overview.md) que atualiza as informações de atualização de dados digitais com base no Hub IOT. Siga as instruções em [*como: ingerir dados do Hub IOT*](how-to-ingest-iot-hub-data.md) para criar esta função do Azure. Reúna o **_nome_** da função para usá-lo neste artigo.

Este exemplo também usa um **simulador de dispositivo** que inclui o provisionamento usando o serviço de provisionamento de dispositivos. O simulador de dispositivo está localizado aqui: [exemplo de integração do Hub IOT e gêmeos digital do Azure](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Obtenha o projeto de exemplo em seu computador navegando até o link de exemplo e selecionando o botão *baixar zip* abaixo do título. Descompacte a pasta baixada.

Você precisará de [**Node.js**](https://nodejs.org/download) instalado em seu computador. O simulador de dispositivo se baseia em **Node.js**, versão 10.0. x ou posterior.

## <a name="solution-architecture"></a>Arquitetura da solução

A imagem abaixo ilustra a arquitetura dessa solução usando o gêmeos digital do Azure com o serviço de provisionamento de dispositivos. Ele mostra o fluxo de provisionamento e desativação do dispositivo.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Diagrama de dispositivo e vários serviços do Azure em um cenário de ponta a ponta. Os dados fluem entre um dispositivo termostato e um DPS. Os dados também fluem do DPS para o Hub IoT e para o Azure digital gêmeos por meio de uma função do Azure rotulada como ' alocação '. Os dados de uma ação manual ' excluir dispositivo ' fluem por meio do Hub IoT > hubs de eventos > Azure Functions > Azure digital gêmeos." lightbox="media/how-to-provision-using-dps/flows.png":::

Este artigo é dividido em duas seções:
* [*Provisionar automaticamente o dispositivo usando o serviço de provisionamento de dispositivos*](#auto-provision-device-using-device-provisioning-service)
* [*Desativar automaticamente o dispositivo usando eventos do ciclo de vida do Hub IoT*](#auto-retire-device-using-iot-hub-lifecycle-events)

Para obter explicações mais detalhadas de cada etapa na arquitetura, consulte as seções individuais mais adiante neste artigo.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Provisionar automaticamente o dispositivo usando o serviço de provisionamento de dispositivos

Nesta seção, você anexará o serviço de provisionamento de dispositivos ao gêmeos digital do Azure para provisionar dispositivos automaticamente por meio do caminho abaixo. Este é um trecho da arquitetura completa mostrada [anteriormente](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Diagrama de fluxo de provisionamento – um trecho do diagrama da arquitetura da solução, com números rotulando seções do fluxo. Os dados fluem entre um dispositivo termostato e um DPS (1 para dispositivos > DPS e 5 para o dispositivo DPS >). Os dados também fluem do DPS para o Hub IoT (4) e para o gêmeos digital do Azure (3) por meio de uma função do Azure rotulada como &quot;alocação&quot; (2)." lightbox="media/how-to-provision-using-dps/provision.png":::

Aqui está uma descrição do fluxo do processo:
1. O dispositivo entra em contato com o ponto de extremidade do DPS, passando informações de identificação para provar sua identidade.
2. O DPS valida a identidade do dispositivo validando a ID de registro e a chave em relação à lista de registro e chama uma [função do Azure](../azure-functions/functions-overview.md) para fazer a alocação.
3. A função do Azure cria um [novo gêmeos](concepts-twins-graph.md) no Azure digital para o dispositivo. O FileUp terá o mesmo nome que a ID de **registro** do dispositivo.
4. O DPS registra o dispositivo com um hub IoT e popula o estado de entrelaçamento desejado do dispositivo.
5. O Hub IoT retorna informações de ID do dispositivo e as informações de conexão do Hub IoT para o dispositivo. Agora, o dispositivo pode se conectar ao Hub IoT.

As seções a seguir percorrem as etapas para configurar esse fluxo de dispositivo de provisionamento automático.

### <a name="create-a-device-provisioning-service"></a>Criar um Serviço de Provisionamento de Dispositivos

Quando um novo dispositivo é provisionado usando o serviço de provisionamento de dispositivos, um novo "or" para esse dispositivo pode ser criado no Azure digital gêmeos com o mesmo nome que a ID de registro.

Crie uma instância do serviço de provisionamento de dispositivos, que será usada para provisionar dispositivos IoT. Você pode usar as instruções de CLI do Azure abaixo ou usar o portal do Azure: [*início rápido: configurar o serviço de provisionamento de dispositivos no Hub IOT com o portal do Azure*](../iot-dps/quick-setup-auto-provision.md).

O comando a seguir CLI do Azure criará um serviço de provisionamento de dispositivos. Você precisará especificar um nome do serviço de provisionamento de dispositivos, um grupo de recursos e uma região. Para ver quais regiões dão suporte ao serviço de provisionamento de dispositivos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
O comando pode ser executado no [Cloud Shell](https://shell.azure.com)ou localmente se você tiver o CLI do Azure [instalado em seu computador](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Adicionar uma função a ser usada com o serviço de provisionamento de dispositivos

Dentro de seu projeto de aplicativo de funções criado na seção [pré-requisitos](#prerequisites) , você criará uma nova função para usar com o serviço de provisionamento de dispositivos. Essa função será usada pelo serviço de provisionamento de dispositivos em uma política de [alocação personalizada](../iot-dps/how-to-use-custom-allocation-policies.md) para provisionar um novo dispositivo.

Comece abrindo o projeto de aplicativo de funções no Visual Studio em seu computador e siga as etapas abaixo.

#### <a name="step-1-add-a-new-function"></a>Etapa 1: adicionar uma nova função 

Adicione uma nova função do tipo *http-Trigger* ao projeto do aplicativo de funções no Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Captura de tela da exibição do Visual Studio para adicionar o Azure function do tipo gatilho http em seu projeto de aplicativo de funções." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Etapa 2: preencher o código de função

Adicione um novo pacote NuGet ao projeto: [Microsoft. Azure. Devices. Provisioning. Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Talvez seja necessário adicionar mais pacotes ao seu projeto também, se os pacotes usados no código ainda não fizerem parte do projeto.

No arquivo de código de função recém-criado, Cole o código a seguir, renomeie a função como *DpsAdtAllocationFunc. cs* e salve o arquivo.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Etapa 3: publicar o aplicativo de funções no Azure

Publique o projeto com a função *DpsAdtAllocationFunc. cs* no aplicativo de funções no Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Criar registro de provisionamento de dispositivos

Em seguida, você precisará criar um registro no serviço de provisionamento de dispositivos usando uma **função de alocação personalizada**. Siga as instruções para fazer isso na seção [*criar o registro*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) do artigo políticas de alocação personalizadas na documentação do serviço de provisionamento de dispositivos.

Ao passar pelo fluxo, certifique-se de selecionar as seguintes opções para vincular o registro à função que você acabou de criar.

* **Selecione como você deseja atribuir dispositivos a hubs**: personalizado (use a função do Azure).
* **Selecione os hubs IOT aos quais este grupo pode ser atribuído:** Escolha o nome do Hub IoT ou selecione o botão *vincular um novo hub IOT* e escolha o Hub IOT na lista suspensa.

Em seguida, escolha o botão *selecionar uma nova função* para vincular seu aplicativo de funções ao grupo de registros. Em seguida, preencha os seguintes valores:

* **Assinatura**: sua assinatura do Azure é preenchida automaticamente. Verifique se ele é a assinatura correta.
* **Aplicativo de funções**: escolha o nome do aplicativo de funções.
* **Função**: escolha DpsAdtAllocationFunc.

Salve seus detalhes.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Captura de tela da janela detalhes do grupo de registro alfandegário para selecionar personalizado (usar a função do Azure) e o nome do Hub IoT nas seções selecione como deseja atribuir dispositivos aos hubs e selecione os hubs IoT aos quais este grupo pode ser atribuído. Além disso, selecione sua assinatura, o aplicativo de funções na lista suspensa e certifique-se de selecionar DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

Depois de criar o registro, a **chave primária** para o registro será usada posteriormente para configurar o simulador de dispositivo para este artigo.

### <a name="set-up-the-device-simulator"></a>Configurar o simulador de dispositivo

Este exemplo usa um simulador de dispositivo que inclui o provisionamento usando o serviço de provisionamento de dispositivos. O simulador de dispositivo está localizado aqui: [exemplo de integração do Hub IOT e gêmeos digital do Azure](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Se você ainda não baixou o exemplo, obtenha-o agora navegando até o link de exemplo e selecionando o botão *baixar zip* abaixo do título. Descompacte a pasta baixada.

#### <a name="upload-the-model"></a>Carregar o modelo

O simulador de dispositivo é um dispositivo de tipo termostato que usa o modelo com essa ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Você precisará carregar esse modelo no Azure digital gêmeos antes de poder criar um texto para o dispositivo.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Para obter mais informações sobre modelos, consulte [*como: gerenciar modelos*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Configurar e executar o simulador

Na janela de comando, navegue até o exemplo baixado do *Azure digital gêmeos e a integração do Hub IOT* que você descompactou anteriormente e, em seguida, no diretório *Device-Simulator* . Em seguida, instale as dependências para o projeto usando o seguinte comando:

```cmd
npm install
```

Em seguida, no diretório do Device Simulator, copie o arquivo. env. Template para um novo arquivo chamado. env e reúna os seguintes valores para preencher as configurações:

* PROVISIONING_IDSCOPE: para obter esse valor, navegue até o serviço de provisionamento de dispositivos no [portal do Azure](https://portal.azure.com/), em seguida, selecione *visão geral* nas opções de menu e procure o *escopo de ID* de campo.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Captura de tela da exibição portal do Azure da página Visão geral de provisionamento de dispositivos para copiar o valor de escopo da ID." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: você pode escolher uma ID de registro para seu dispositivo.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: essa é a chave primária para o registro que você configurou anteriormente. Para obter esse valor novamente, navegue até o serviço de provisionamento de dispositivos no portal do Azure, selecione *gerenciar registros* e, em seguida, selecione o grupo de registros que você criou anteriormente e copie a *chave primária*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Captura de tela da exibição portal do Azure da página gerenciar registros do serviço de provisionamento de dispositivos para copiar o valor da chave primária SAS." lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

Agora, use os valores acima para atualizar as configurações de arquivo. env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Salve e feche o arquivo.

### <a name="start-running-the-device-simulator"></a>Iniciar a execução do simulador de dispositivo

Ainda no diretório *Device-Simulator* na janela de comando, inicie o simulador de dispositivo usando o seguinte comando:

```cmd
node .\adt_custom_register.js
```

Você deve ver o dispositivo que está sendo registrado e conectado ao Hub IoT e, em seguida, começar a enviar mensagens.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Captura de tela da janela Comando mostrando o registro do dispositivo e o envio de mensagens" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Validar

Como resultado do fluxo que você configurou neste artigo, o dispositivo será registrado automaticamente no Azure digital gêmeos. Use o seguinte comando da [CLI do Azure digital gêmeos](how-to-use-cli.md) para localizar o cópia do dispositivo na instância do gêmeos digital do Azure que você criou.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Você deve ver o cópia do dispositivo que está sendo encontrado na instância do gêmeos digital do Azure.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Captura de tela da janela Comando mostrando o &quot;myshow recém-criado&quot;." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Desativar automaticamente o dispositivo usando eventos do ciclo de vida do Hub IoT

Nesta seção, você anexará eventos de ciclo de vida do Hub IoT ao Azure digital gêmeos para desativar automaticamente os dispositivos por meio do caminho abaixo. Este é um trecho da arquitetura completa mostrada [anteriormente](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Diagrama do fluxo de desativação do dispositivo – um trecho do diagrama da arquitetura da solução, com números rotulando seções do fluxo. O dispositivo termostato é mostrado sem conexões com os serviços do Azure no diagrama. Os dados de uma ação manual ' excluir dispositivo ' fluem pelo Hub IoT (1) > hubs de eventos (2) > Azure Functions > Azure digital gêmeos (3)." lightbox="media/how-to-provision-using-dps/retire.png":::

Aqui está uma descrição do fluxo do processo:
1. Um processo externo ou manual dispara a exclusão de um dispositivo no Hub IoT.
2. O Hub IoT exclui o dispositivo e gera um evento de [ciclo de vida do dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) que será roteado para um hub de [eventos](../event-hubs/event-hubs-about.md).
3. Uma função do Azure exclui o "gêmeos" do dispositivo no Azure digital.

As seções a seguir percorrem as etapas para configurar esse fluxo de dispositivo de desativação automática.

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Em seguida, você criará um [Hub de eventos](../event-hubs/event-hubs-about.md) do Azure para receber eventos do ciclo de vida do Hub IOT. 

Siga as etapas descritas no início rápido [*criar um hub de eventos*](../event-hubs/event-hubs-create.md) . Nomeie o *lifecycleevents* do hub de eventos. Você usará esse nome de Hub de eventos quando configurar a rota do Hub IoT e uma função do Azure nas próximas seções.

A captura de tela abaixo ilustra a criação do hub de eventos.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Captura de tela da janela de portal do Azure para criar um hub de eventos com o nome lifecycleevents." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Criar política SAS para seu hub de eventos

Em seguida, você precisará criar uma [política de SAS (assinatura de acesso compartilhado)](../event-hubs/authorize-access-shared-access-signature.md) para configurar o Hub de eventos com seu aplicativo de funções.
Para fazer isso,
1. Navegue até o Hub de eventos que você acabou de criar no portal do Azure e selecione **políticas de acesso compartilhado** nas opções de menu à esquerda.
2. Selecione **Adicionar**. Na janela *Adicionar política SAS* que é aberta, insira um nome de política de sua escolha e marque a caixa de seleção *escutar* .
3. Selecione **Criar**.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Captura de tela da portal do Azure para adicionar uma política SAS do hub de eventos." lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Configurar o Hub de eventos com o aplicativo de funções

Em seguida, configure o aplicativo de funções do Azure que você configurou na seção [pré-requisitos](#prerequisites) para trabalhar com o novo hub de eventos. Você fará isso definindo uma variável de ambiente dentro do aplicativo de funções com a cadeia de conexão do hub de eventos.

1. Abra a política que você acabou de criar e copie o valor da **chave primária da cadeia de conexão** .

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Captura de tela da portal do Azure para copiar a chave primária da cadeia de conexão." lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Adicione a cadeia de conexão como uma variável nas configurações do aplicativo de funções com o comando CLI do Azure a seguir. O comando pode ser executado no [Cloud Shell](https://shell.azure.com)ou localmente se você tiver o CLI do Azure [instalado em seu computador](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Adicionar uma função a ser desativada com eventos do ciclo de vida do Hub IoT

Dentro de seu projeto de aplicativo de funções que você criou na seção [pré-requisitos](#prerequisites) , você criará uma nova função para desativar um dispositivo existente usando eventos do ciclo de vida do Hub IOT.

Para obter mais informações sobre eventos do ciclo de vida, consulte [*eventos de não telemetria do Hub IOT*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para obter mais informações sobre como usar hubs de eventos com o Azure functions, consulte [*gatilho de hubs de eventos do Azure para Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Comece abrindo o projeto de aplicativo de funções no Visual Studio em seu computador e siga as etapas abaixo.

#### <a name="step-1-add-a-new-function"></a>Etapa 1: adicionar uma nova função
     
Adicione uma nova função do tipo *gatilho do hub de eventos* ao projeto do aplicativo de funções no Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Captura de tela da janela do Visual Studio para adicionar uma função do Azure do tipo gatilho de Hub de eventos em seu projeto de aplicativo de funções." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Etapa 2: preencher o código de função

No arquivo de código de função recém-criado, Cole o código a seguir, renomeie a função como `DeleteDeviceInTwinFunc.cs` e salve o arquivo.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Etapa 3: publicar o aplicativo de funções no Azure

Publique o projeto com a função *DeleteDeviceInTwinFunc. cs* no aplicativo de funções no Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Criar uma rota do Hub IoT para eventos de ciclo de vida

Agora, você configurará uma rota do Hub IoT para rotear eventos de ciclo de vida do dispositivo. Nesse caso, você vai ouvir especificamente os eventos de exclusão de dispositivos, identificados por `if (opType == "deleteDeviceIdentity")` . Isso disparará a exclusão do item de p digital, finalizando a desativação de um dispositivo e seu "up digital".

Primeiro, você precisará criar um ponto de extremidade do hub de eventos em seu hub IoT. Em seguida, você adicionará uma rota no Hub IoT para enviar eventos de ciclo de vida para esse ponto de extremidade do hub de eventos.
Siga estas etapas para criar um ponto de extremidade do hub de eventos:

1. No [portal do Azure](https://portal.azure.com/), navegue até o Hub IOT criado na seção [pré-requisitos](#prerequisites) e selecione roteamento de **mensagens** nas opções de menu à esquerda.
2. Selecione a guia **Pontos de extremidade personalizados**.
3. Selecione **+ Adicionar** e escolha os **hubs de eventos** para adicionar um ponto de extremidade de tipo de hubs de eventos.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Captura de tela da janela do Visual Studio para adicionar um ponto de extremidade personalizado do hub de eventos." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. Na janela *Adicionar um ponto de extremidade do hub de eventos* que é aberto, escolha os seguintes valores:
    * **Nome do ponto de extremidade**: escolha um nome de ponto de extremidade.
    * **Namespace do hub de eventos**: selecione o namespace do hub de eventos na lista suspensa.
    * **Instância do hub de eventos**: escolha o nome do hub de eventos que você criou na etapa anterior.
5. Selecione **Criar**. Mantenha essa janela aberta para adicionar uma rota na próxima etapa.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Captura de tela da janela do Visual Studio para adicionar um ponto de extremidade do hub de eventos." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Em seguida, você adicionará uma rota que se conecta ao ponto de extremidade criado na etapa acima, com uma consulta de roteamento que envia os eventos de exclusão. Siga estas etapas para criar uma rota:

1. Navegue até a guia *rotas* e selecione **Adicionar** para adicionar uma rota.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Captura de tela da janela do Visual Studio para adicionar uma rota para enviar eventos." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. Na página *Adicionar uma rota* que é aberta, escolha os seguintes valores:

   * **Nome**: escolha um nome para a rota. 
   * **Ponto de extremidade**: escolha o ponto de extremidade dos hubs de eventos que você criou anteriormente na lista suspensa.
   * **Fonte de dados**: escolha *eventos de ciclo de vida do dispositivo*.
   * **Consulta de roteamento**: insira `opType='deleteDeviceIdentity'` . Essa consulta limita os eventos de ciclo de vida do dispositivo para enviar apenas os eventos de exclusão.

3. Clique em **Salvar**.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Captura de tela da janela de portal do Azure para adicionar uma rota para enviar eventos de ciclo de vida." lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

Depois de ter passado por esse fluxo, tudo é definido para desativar dispositivos de ponta a ponta.

### <a name="validate"></a>Validar

Para disparar o processo de desativação, você precisa excluir manualmente o dispositivo do Hub IoT.

Você pode fazer isso com um [comando CLI do Azure](/cli/azure/ext/azure-iot/iot/hub/module-identity#ext_azure_iot_az_iot_hub_module_identity_delete) ou na portal do Azure. Siga as etapas abaixo para excluir o dispositivo no portal do Azure:

1. Navegue até o Hub IoT e escolha **dispositivos IOT** nas opções de menu à esquerda. 
2. Você verá um dispositivo com a ID de registro do dispositivo escolhida na [primeira metade deste artigo](#auto-provision-device-using-device-provisioning-service). Como alternativa, você pode escolher qualquer outro dispositivo a ser excluído, desde que ele tenha um vertical no Azure digital gêmeos, para que você possa verificar se o vertical é excluído automaticamente depois que o dispositivo é excluído.
3. Selecione o dispositivo e escolha **excluir**.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Captura de tela do portal do Azure para excluir o dispositivo de dispositivos de IoT." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Pode levar alguns minutos para ver as alterações refletidas no Azure digital gêmeos.

Use o seguinte comando da [CLI do Azure digital gêmeos](how-to-use-cli.md) para verificar se o cópia do dispositivo na instância do gêmeos digital do Azure foi excluída.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Você verá que o cópia do dispositivo não pode mais ser encontrado na instância do gêmeos digital do Azure.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Captura de tela do janela Comando mostrando o ' fileshow ' não encontrado." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais dos recursos criados neste artigo, siga estas etapas para excluí-los.

Usando o CLI do Azure de Azure Cloud Shell ou local, você pode excluir todos os recursos do Azure em um grupo de recursos com o comando [AZ Group Delete](/cli/azure/group#az-group-delete) . Isso remove o grupo de recursos; a instância do gêmeos digital do Azure; o Hub IoT e o registro do dispositivo de Hub; o tópico da grade de eventos e as assinaturas associadas; o namespace de hubs de eventos e ambos os Azure Functions aplicativos, incluindo recursos associados, como armazenamento.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Em seguida, exclua a pasta de exemplo do projeto que você baixou de seu computador local.

## <a name="next-steps"></a>Próximas etapas

O digital gêmeos criado para os dispositivos é armazenado como uma hierarquia simples no Azure digital gêmeos, mas eles podem ser aprimorados com informações de modelo e uma hierarquia de vários níveis para a organização. Para saber mais sobre esse conceito, leia:

* [*Conceitos: digital gêmeos e o gráfico de entrelaçamento*](concepts-twins-graph.md)

Para obter mais informações sobre como usar solicitações HTTP com o Azure functions, consulte:

* [*Gatilho de solicitação HTTP do Azure para Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Você pode escrever uma lógica personalizada para fornecer automaticamente essas informações usando os dados do modelo e do grafo já armazenados no Azure digital gêmeos. Para ler mais sobre como gerenciar, atualizar e recuperar informações do grafo gêmeos, consulte o seguinte:

* [*Como gerenciar um teledigital*](how-to-manage-twin.md)
* [*Como consultar o gráfico de entrelaçamento*](how-to-query-graph.md)