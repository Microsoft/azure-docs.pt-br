---
title: Gerenciar dispositivos automaticamente usando o DPS
titleSuffix: Azure Digital Twins
description: Veja como configurar um processo automatizado para provisionar e desativar dispositivos IoT no Azure digital gêmeos usando o DPS (serviço de provisionamento de dispositivos).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c2e7c9c96f237512d7f28f7243707b097c034aab
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198447"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Gerenciar dispositivos automaticamente no gêmeos digital do Azure usando o DPS (serviço de provisionamento de dispositivos)

Neste artigo, você aprenderá a integrar o Azure digital gêmeos ao [DPS (serviço de provisionamento de dispositivos)](../iot-dps/about-iot-dps.md).

A solução descrita neste artigo permitirá que você automatize o processo para **_provisionar_** e **_desativar_** dispositivos do Hub IOT no gêmeos digital do Azure usando o serviço de provisionamento de dispositivos. 

Para obter mais informações sobre os estágios de _provisionamento_ e _desativação_ , e entender melhor o conjunto de estágios gerais de gerenciamento de dispositivos que são comuns a todos os projetos de IOT corporativos, consulte a [seção *ciclo de vida do dispositivo*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) da documentação de gerenciamento de dispositivos do Hub IOT.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar o provisionamento, você precisa ter uma **instância do gêmeos digital do Azure** que contenha modelos e gêmeos. Essa instância também deve ser configurada com a capacidade de atualizar informações de cópia digital com base nos dados. 

Se você ainda não tiver essa configuração, poderá criá-la seguindo o tutorial do Azure digital gêmeos [*: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md). O tutorial irá orientá-lo na configuração de uma instância do gêmeos digital do Azure com modelos e gêmeos, um [Hub IOT](../iot-hub/about-iot-hub.md)do Azure conectado e várias [funções do Azure](../azure-functions/functions-overview.md) para propagar o fluxo de dados.

Você precisará dos seguintes valores posteriormente neste artigo de quando você configurar sua instância. Se você precisar reunir esses valores novamente, use os links abaixo para obter instruções.
* A instância de Gêmeos Digitais do Azure **_nome do host_** ([localizar no portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_Cadeia de conexão_** da cadeia de conexão dos hubs de eventos do Azure ([Localizar no portal](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

Este exemplo também usa um **simulador de dispositivo** que inclui o provisionamento usando o serviço de provisionamento de dispositivos. O simulador de dispositivo está localizado aqui: [exemplo de integração do Hub IOT e gêmeos digital do Azure](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Obtenha o projeto de exemplo em seu computador navegando até o link de exemplo e selecionando o botão *baixar zip* abaixo do título. Descompacte a pasta baixada.

O simulador de dispositivo se baseia em **Node.js**, versão 10.0. x ou posterior. [*Preparar seu ambiente de desenvolvimento*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) descreve como instalar Node.js para este tutorial no Windows ou no Linux.

## <a name="solution-architecture"></a>Arquitetura da solução

A imagem abaixo ilustra a arquitetura dessa solução usando o gêmeos digital do Azure com o serviço de provisionamento de dispositivos. Ele mostra o fluxo de provisionamento e desativação do dispositivo.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Uma exibição de um dispositivo e vários serviços do Azure em um cenário de ponta a ponta. Os dados fluem entre um dispositivo termostato e um DPS. Os dados também fluem do DPS para o Hub IoT e para o Azure digital gêmeos por meio de uma função do Azure rotulada como ' alocação '. Os dados de uma ação manual ' excluir dispositivo ' fluem por meio do Hub IoT > hubs de eventos > Azure Functions > Azure digital gêmeos.":::

Este artigo é dividido em duas seções:
* [*Provisionar automaticamente o dispositivo usando o serviço de provisionamento de dispositivos*](#auto-provision-device-using-device-provisioning-service)
* [*Desativar automaticamente o dispositivo usando eventos do ciclo de vida do Hub IoT*](#auto-retire-device-using-iot-hub-lifecycle-events)

Para obter explicações mais detalhadas de cada etapa na arquitetura, consulte as seções individuais mais adiante neste artigo.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Provisionar automaticamente o dispositivo usando o serviço de provisionamento de dispositivos

Nesta seção, você anexará o serviço de provisionamento de dispositivos ao gêmeos digital do Azure para provisionar dispositivos automaticamente por meio do caminho abaixo. Este é um trecho da arquitetura completa mostrada [anteriormente](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Provisionar fluxo--um trecho do diagrama da arquitetura da solução, com números rotulando seções do fluxo. Os dados fluem entre um dispositivo termostato e um DPS (1 para dispositivos > DPS e 5 para o dispositivo DPS >). Os dados também fluem do DPS para o Hub IoT (4) e para o gêmeos digital do Azure (3) por meio de uma função do Azure rotulada como &quot;alocação&quot; (2).":::

Aqui está uma descrição do fluxo do processo:
1. O dispositivo entra em contato com o ponto de extremidade do DPS, passando informações de identificação para provar sua identidade.
2. O DPS valida a identidade do dispositivo validando a ID de registro e a chave em relação à lista de registro e chama uma [função do Azure](../azure-functions/functions-overview.md) para fazer a alocação.
3. A função do Azure cria um [novo gêmeos](concepts-twins-graph.md) no Azure digital para o dispositivo.
4. O DPS registra o dispositivo com um hub IoT e popula o estado de entrelaçamento desejado do dispositivo.
5. O Hub IoT retorna informações de ID do dispositivo e as informações de conexão do Hub IoT para o dispositivo. Agora, o dispositivo pode se conectar ao Hub IoT.

As seções a seguir percorrem as etapas para configurar esse fluxo de dispositivo de provisionamento automático.

### <a name="create-a-device-provisioning-service"></a>Criar um Serviço de Provisionamento de Dispositivos

Quando um novo dispositivo é provisionado usando o serviço de provisionamento de dispositivos, é possível criar um novo "." para esse dispositivo no Azure digital gêmeos.

Crie uma instância do serviço de provisionamento de dispositivos, que será usada para provisionar dispositivos IoT. Você pode usar as instruções de CLI do Azure abaixo ou usar o portal do Azure: [*início rápido: configurar o serviço de provisionamento de dispositivos no Hub IOT com o portal do Azure*](../iot-dps/quick-setup-auto-provision.md).

O comando a seguir CLI do Azure criará um serviço de provisionamento de dispositivos. Será necessário especificar um nome, um grupo de recursos e uma região. O comando pode ser executado no [Cloud Shell](https://shell.azure.com)ou localmente se você tiver o CLI do Azure [instalado em seu computador](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Criar uma função do Azure

Em seguida, você criará uma função HTTP disparada por solicitação dentro de um aplicativo de funções. Você pode usar o aplicativo de funções criado no tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)) ou seu próprio.

Essa função será usada pelo serviço de provisionamento de dispositivos em uma política de [alocação personalizada](../iot-dps/how-to-use-custom-allocation-policies.md) para provisionar um novo dispositivo. Para obter mais informações sobre como usar solicitações HTTP com o Azure functions, consulte [*gatilho de solicitação de http do Azure para Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md).

Dentro de seu projeto de aplicativo de funções, adicione uma nova função. Além disso, adicione um novo pacote NuGet ao projeto: `Microsoft.Azure.Devices.Provisioning.Service` .

No arquivo de código de função recém-criado, Cole o código a seguir.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

Salve o arquivo e, em seguida, publique novamente seu aplicativo de funções. Para obter instruções sobre como publicar o aplicativo de funções, consulte a seção [*publicar o aplicativo*](tutorial-end-to-end.md#publish-the-app) do tutorial de ponta a ponta.

### <a name="configure-your-function"></a>Configurar a função

Em seguida, você precisará definir variáveis de ambiente em seu aplicativo de funções anteriormente, contendo a referência à instância de gêmeos digital do Azure que você criou. Se você usou o tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)), a configuração já estará configurada.

Adicione a configuração com este CLI do Azure comando:

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Verifique se as permissões e a atribuição da função de identidade gerenciada estão configuradas corretamente para o aplicativo de funções, conforme descrito na seção [*atribuir permissões ao aplicativo de funções*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) no tutorial de ponta a ponta.

### <a name="create-device-provisioning-enrollment"></a>Criar registro de provisionamento de dispositivos

Em seguida, você precisará criar um registro no serviço de provisionamento de dispositivos usando uma **função de alocação personalizada**. Siga as instruções para fazer isso nas seções [*criar o registro*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) e [*derivar chaves de dispositivo exclusivas*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) do artigo serviços de provisionamento de dispositivos sobre políticas de alocação personalizadas.

Ao passar pelo fluxo, você vinculará o registro à função que acabou de criar selecionando a função durante a etapa para **selecionar como deseja atribuir dispositivos a hubs**. Depois de criar o registro, o nome de registro e a chave SAS primária ou secundária serão usados posteriormente para configurar o simulador de dispositivo para este artigo.

### <a name="set-up-the-device-simulator"></a>Configurar o simulador de dispositivo

Este exemplo usa um simulador de dispositivo que inclui o provisionamento usando o serviço de provisionamento de dispositivos. O simulador de dispositivo está localizado aqui: [exemplo de integração do Hub IOT e gêmeos digital do Azure](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Se você ainda não baixou o exemplo, obtenha-o agora navegando até o link de exemplo e selecionando o botão *baixar zip* abaixo do título. Descompacte a pasta baixada.

Abra uma janela de comando e navegue até a pasta baixada e, em seguida, no diretório *Device-Simulator* . Instale as dependências do projeto usando o seguinte comando:

```cmd
npm install
```

Em seguida, copie o arquivo *. env. Template* para um novo arquivo chamado *. env* e preencha estas configurações:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Salve e feche o arquivo.

### <a name="start-running-the-device-simulator"></a>Iniciar a execução do simulador de dispositivo

Ainda no diretório *Device-Simulator* na janela de comando, inicie o simulador de dispositivo usando o seguinte comando:

```cmd
node .\adt_custom_register.js
```

Você deve ver o dispositivo que está sendo registrado e conectado ao Hub IoT e, em seguida, começar a enviar mensagens.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="janela Comando mostrando o registro de dispositivo e enviando mensagens":::

### <a name="validate"></a>Validar

Como resultado do fluxo que você configurou neste artigo, o dispositivo será registrado automaticamente no Azure digital gêmeos. Usando o seguinte comando da [CLI do Azure digital gêmeos](how-to-use-cli.md) para localizar o cópia do dispositivo na instância do gêmeos digital do Azure que você criou.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Você deve ver o cópia do dispositivo que está sendo encontrado na instância do gêmeos digital do Azure.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="janela Comando mostrando o matreme recém-criado":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Desativar automaticamente o dispositivo usando eventos do ciclo de vida do Hub IoT

Nesta seção, você anexará eventos de ciclo de vida do Hub IoT ao Azure digital gêmeos para desativar automaticamente os dispositivos por meio do caminho abaixo. Este é um trecho da arquitetura completa mostrada [anteriormente](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Desativar o fluxo do dispositivo – um trecho do diagrama da arquitetura da solução, com números rotulando seções do fluxo. O dispositivo termostato é mostrado sem conexões com os serviços do Azure no diagrama. Os dados de uma ação manual ' excluir dispositivo ' fluem pelo Hub IoT (1) > hubs de eventos (2) > Azure Functions > Azure digital gêmeos (3).":::

Aqui está uma descrição do fluxo do processo:
1. Um processo externo ou manual dispara a exclusão de um dispositivo no Hub IoT.
2. O Hub IoT exclui o dispositivo e gera um evento de [ciclo de vida do dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) que será roteado para um hub de [eventos](../event-hubs/event-hubs-about.md).
3. Uma função do Azure exclui o "gêmeos" do dispositivo no Azure digital.

As seções a seguir percorrem as etapas para configurar esse fluxo de dispositivo de desativação automática.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Agora você precisa criar um hub de [eventos](../event-hubs/event-hubs-about.md)do Azure, que será usado para receber os eventos do ciclo de vida do Hub IOT. 

Percorra as etapas descritas no início rápido [*criar um hub de eventos*](../event-hubs/event-hubs-create.md) , usando as seguintes informações:
* Se você estiver usando o tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)), poderá reutilizar o grupo de recursos criado para o tutorial de ponta a ponta.
* Nomeie seu *lifecycleevents* de Hub de eventos ou outra coisa de sua escolha e lembre-se do namespace que você criou. Você os usará quando configurar a função de ciclo de vida e a rota do Hub IoT nas próximas seções.

### <a name="create-an-azure-function"></a>Criar uma função do Azure

Em seguida, você criará uma função disparada pelos Hubs de Eventos dentro de um aplicativo de funções. Você pode usar o aplicativo de funções criado no tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)) ou seu próprio. 

Dê um nome ao seu hub de eventos disparado *lifecycleevents* e conecte o gatilho do hub de eventos ao Hub de eventos criado na etapa anterior. Se você usou um nome de Hub de eventos diferente, altere-o para corresponder ao nome do gatilho abaixo.

Esta função usará o evento de ciclo de vida do dispositivo do Hub IoT para desativar um dispositivo existente. Para obter mais informações sobre eventos do ciclo de vida, consulte [*eventos de não telemetria do Hub IOT*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para obter mais informações sobre como usar hubs de eventos com o Azure functions, consulte [*gatilho de hubs de eventos do Azure para Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dentro de seu aplicativo de funções publicado, adicione uma nova classe de função do tipo *gatilho de Hub de eventos* e cole no código abaixo.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

Salve o projeto e, em seguida, publique o aplicativo de funções novamente. Para obter instruções sobre como publicar o aplicativo de funções, consulte a seção [*publicar o aplicativo*](tutorial-end-to-end.md#publish-the-app) do tutorial de ponta a ponta.

### <a name="configure-your-function"></a>Configurar a função

Em seguida, você precisará definir variáveis de ambiente em seu aplicativo de funções anteriormente, contendo a referência à instância de gêmeos digital do Azure que você criou e o Hub de eventos. Se você usou o tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md)), a primeira configuração já estará configurada.

Adicione a configuração com este comando CLI do Azure. O comando pode ser executado no [Cloud Shell](https://shell.azure.com)ou localmente se você tiver o CLI do Azure [instalado em seu computador](/cli/azure/install-azure-cli).

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Em seguida, você precisará configurar a variável de ambiente de função para se conectar ao Hub de eventos recém-criado.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Verifique se as permissões e a atribuição da função de identidade gerenciada estão configuradas corretamente para o aplicativo de funções, conforme descrito na seção [*atribuir permissões ao aplicativo de funções*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) no tutorial de ponta a ponta.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Criar uma rota do Hub IoT para eventos de ciclo de vida

Agora você precisa configurar uma rota do Hub IoT para rotear eventos de ciclo de vida do dispositivo. Nesse caso, você vai ouvir especificamente os eventos de exclusão de dispositivos, identificados por `if (opType == "deleteDeviceIdentity")` . Isso disparará a exclusão do item de p digital, finalizando a desativação de um dispositivo e seu "up digital".

As instruções para criar uma rota do Hub IoT são descritas neste artigo: [*usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade*](../iot-hub/iot-hub-devguide-messages-d2c.md). A seção *eventos de não telemetria* explica que você pode usar **eventos de ciclo de vida do dispositivo** como a fonte de dados para a rota.

As etapas que você precisa seguir para essa configuração são:
1. Crie um ponto de extremidade personalizado do hub de eventos do Hub IoT. Esse ponto de extremidade deve ser direcionado ao Hub de eventos criado na seção [*criar um hub de eventos*](#create-an-event-hub) .
2. Adicionar uma rota de *eventos de ciclo de vida do dispositivo* . Use o ponto de extremidade criado na etapa anterior. Você pode limitar os eventos de ciclo de vida do dispositivo para enviar somente os eventos de exclusão adicionando a consulta de roteamento `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Adicionar uma rota":::

Depois de ter passado por esse fluxo, tudo é definido para desativar dispositivos de ponta a ponta.

### <a name="validate"></a>Validar

Para disparar o processo de desativação, você precisa excluir manualmente o dispositivo do Hub IoT.

Na [primeira metade deste artigo](#auto-provision-device-using-device-provisioning-service), você criou um dispositivo no Hub IOT e um teledigital correspondente. 

Agora, vá para o Hub IoT e exclua esse dispositivo (você pode fazer isso com um [comando CLI do Azure](/cli/azure/ext/azure-iot/iot/hub/module-identity#ext_azure_iot_az_iot_hub_module_identity_delete) ou na [portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

O dispositivo será removido automaticamente do Azure digital gêmeos. 

Use o seguinte comando da [CLI do Azure digital gêmeos](how-to-use-cli.md) para verificar se o cópia do dispositivo na instância do gêmeos digital do Azure foi excluída.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Você verá que o cópia do dispositivo não pode mais ser encontrado na instância do gêmeos digital do Azure.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="janela Comando mostrando não encontrado":::

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

Você pode escrever uma lógica personalizada para fornecer automaticamente essas informações usando os dados do modelo e do grafo já armazenados no Azure digital gêmeos. Para ler mais sobre como gerenciar, atualizar e recuperar informações do grafo gêmeos, consulte o seguinte:

* [*Como gerenciar um teledigital*](how-to-manage-twin.md)
* [*Como consultar o gráfico de entrelaçamento*](how-to-query-graph.md)