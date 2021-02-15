---
title: Integrar com o Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Consulte como configurar rotas de eventos do Azure digital gêmeos para Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 951c52cdba191aa291061259e1c15b9190513770
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092693"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrar o gêmeos digital do Azure ao Azure Time Series Insights

Neste artigo, você aprenderá a integrar o gêmeos digital do Azure ao [TSI (Azure Time Series insights)](../time-series-insights/overview-what-is-tsi.md).

A solução descrita neste artigo permitirá que você reúna e analise dados históricos sobre sua solução de IoT. Os Gêmeos Digitais do Azure são uma ótima opção para alimentar dados no Time Series Insights, pois permite que você correlacione vários fluxos de dados e padronize suas informações antes de enviá-las para o Time Series Insights. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma relação com Time Series Insights, você precisa ter uma **instância de gêmeos digital do Azure**. Essa instância deve ser configurada com a capacidade de atualizar informações de cópia digital com base nos dados, pois você precisará atualizar informações de vertical algumas vezes para ver os dados rastreados no Time Series Insights. 

Se você ainda não tiver essa configuração, poderá criá-la seguindo o tutorial do Azure digital gêmeos [*: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md). O tutorial orientará você na configuração de uma instância do gêmeos digital do Azure que funciona com um dispositivo IoT virtual para disparar atualizações de atualização de cópia digital.

## <a name="solution-architecture"></a>Arquitetura da solução

Você estará anexando Time Series Insights ao Azure digital gêmeos por meio do caminho abaixo.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Uma exibição dos serviços do Azure em um cenário de ponta a ponta, realçando Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Criar uma rota e um filtro para as notificações de atualização do gêmeo

As instâncias de gêmeos digitais do Azure podem emitir [eventos de atualização de entrelaçar](how-to-interpret-event-data.md) sempre que um estado de entrelaçamento é atualizado. Nesta seção, você criará uma [**rota de eventos**](concepts-route-events.md) gêmeos digital do Azure que direcionará esses eventos de atualização para os [hubs de eventos](../event-hubs/event-hubs-about.md) do Azure para processamento adicional.

O tutorial do Azure digital gêmeos [*: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md) percorre um cenário em que um termômetro é usado para atualizar um atributo de temperatura em uma conexão digital que representa uma sala. Esse padrão se baseia nas atualizações de atualização, em vez de encaminhar a telemetria de um dispositivo IoT, o que oferece a você a flexibilidade de alterar a fonte de dados subjacente sem a necessidade de atualizar sua lógica de Time Series Insights.

1. Primeiro, crie um namespace de Hub de eventos que receberá eventos de sua instância de gêmeos digital do Azure. Você pode usar as instruções de CLI do Azure abaixo ou usar o portal do Azure: [*início rápido: criar um hub de eventos usando portal do Azure*](../event-hubs/event-hubs-create.md). Para ver quais regiões oferecem suporte a hubs de eventos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. Crie um hub de eventos dentro do namespace para receber eventos de alteração de entrelaçamento. Especifique um nome para o Hub de eventos.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Crie uma [regra de autorização](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e recebimento. Especifique um nome para a regra.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Crie um [ponto de extremidade](concepts-route-events.md#create-an-endpoint) de gêmeos digital do Azure que vincula seu hub de eventos à instância do gêmeos digital do Azure.

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. Crie uma [rota](concepts-route-events.md#create-an-event-route) nos Gêmeos Digitais do Azure para enviar eventos de atualização do gêmeo para seu ponto de extremidade. O filtro nessa rota permitirá que somente as mensagens de atualização de entrelaçamento sejam passadas para o ponto de extremidade.

    >[!NOTE]
    >Atualmente, há um **problema conhecido** no Cloud Shell afetando estes grupos de comandos: `az dt route`, `az dt model` e `az dt twin`.
    >
    >Para resolver, execute `az login` no Cloud Shell antes de executar o comando ou use a [CLI local](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) em vez do Cloud Shell. Para obter mais detalhes sobre isso, confira [*Solução de problemas: Problemas conhecidos nos Gêmeos Digitais do Azure*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Antes de prosseguir, anote o seu *namespace de hubs de eventos* e o grupo de *recursos*, pois você irá usá-los novamente para criar outro hub de eventos posteriormente neste artigo.

## <a name="create-a-function-in-azure"></a>Criar uma função no Azure

Em seguida, você usará Azure Functions para criar uma **função disparada por hubs de eventos** dentro de um aplicativo de funções. Você pode usar o aplicativo de funções criado no tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md)) ou seu próprio. 

Essa função converterá esses eventos de atualização de alto% do formulário original como documentos de patch JSON em objetos JSON, contendo apenas valores atualizados e adicionados de seu gêmeos.

Para obter mais informações sobre como usar os hubs de eventos com Azure Functions, consulte [*gatilho de hubs de eventos do Azure para Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dentro de seu aplicativo de funções publicado, adicione uma nova função chamada **ProcessDTUpdatetoTSI** com o código a seguir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>Talvez seja necessário adicionar os pacotes ao seu projeto usando o `dotnet add package` comando ou o Gerenciador de pacotes NuGet do Visual Studio.

Em seguida, **publique** a nova função do Azure. Para obter instruções sobre como fazer isso, consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md#publish-the-function-app-to-azure).

Olhando adiante, essa função enviará os objetos JSON que ele cria para um segundo Hub de eventos, ao qual você se conectará ao Time Series Insights. Você criará esse Hub de eventos na próxima seção.

Posteriormente, você também definirá algumas variáveis de ambiente que esta função usará para se conectar aos seus hubs de eventos próprios.

## <a name="send-telemetry-to-an-event-hub"></a>Enviar telemetria para um hub de eventos

Agora, você criará um segundo Hub de eventos e configurará sua função para transmitir sua saída para o Hub de eventos. Depois, esse hub de eventos será conectado ao Time Series Insights.

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Para criar o segundo Hub de eventos, você pode usar as instruções de CLI do Azure abaixo ou usar o portal do Azure: [*início rápido: criar um hub de eventos usando portal do Azure*](../event-hubs/event-hubs-create.md).

1. Prepare o *namespace dos hubs de eventos* e o nome do *grupo de recursos* do anterior neste artigo

2. Criar um hub de eventos. Especifique um nome para o Hub de eventos.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Crie uma [regra de autorização](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e recebimento. Especifique um nome para a regra.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Configurar a função

Em seguida, você precisará definir variáveis de ambiente em seu aplicativo de funções anteriormente, contendo as cadeias de conexão para os hubs de eventos que você criou.

### <a name="set-the-twins-event-hub-connection-string"></a>Definir a cadeia de conexão do hub de eventos dos Gêmeos

1. Obtenha a [cadeia de conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md)gêmeos usando as regras de autorização que você criou acima para o Hub gêmeos.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Use o valor *primaryConnectionString* do resultado para criar uma configuração de aplicativo em seu aplicativo de funções que contém a cadeia de conexão:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Definir a cadeia de conexão do hub de eventos do Time Series Insights

1. Obtenha a [cadeia de conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md)TSI usando as regras de autorização que você criou acima para o hub de time Series insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Use o valor *primaryConnectionString* do resultado para criar uma configuração de aplicativo em seu aplicativo de funções que contém a cadeia de conexão:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Criar e conectar uma instância do Time Series Insights

Em seguida, você irá configurar uma instância de Time Series Insights para receber os dados do seu segundo Hub de eventos (TSI). Siga as etapas abaixo e para obter mais detalhes sobre esse processo, consulte [*tutorial: configurar um ambiente de Azure Time Series insights Gen2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. Na portal do Azure, comece a criar um ambiente de Time Series Insights. 
    1. Selecione o tipo de preço **Gen2 (L1)** .
    2. Será necessário escolher uma ID de **série temporal** para esse ambiente. Sua ID de série temporal pode ter até três valores que você usará para pesquisar seus dados em Time Series Insights. Para este tutorial, você pode usar **$dtId**. Leia mais sobre como selecionar um valor de ID nas [*práticas recomendadas para escolher uma ID de série temporal*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="O UX do portal de criação para um ambiente de Time Series Insights. Selecione sua assinatura, grupo de recursos e local nas respectivas listas suspensas e escolha um nome para o seu ambiente." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="O UX do portal de criação para um ambiente de Time Series Insights. O tipo de preço Gen2 (L1) é selecionado e o nome da propriedade ID da série temporal é $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Selecione **Avançar: origem do evento** e selecione as informações do hub de eventos TSI anteriores. Você também precisará criar um novo grupo de consumidores de hubs de eventos.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="O UX do portal de criação para uma fonte de evento de Time Series Insights ambiente. Você está criando uma origem do evento com as informações do hub de eventos acima. Você também está criando um novo grupo de consumidores." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Começar a enviar dados de IoT para o Azure digital gêmeos

Para começar a enviar dados para Time Series Insights, você precisará começar a atualizar as propriedades de atualização de troca digital no Azure digital gêmeos com valores de dados em alteração. Use o comando [AZ DT myupdate](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update) .

Se você estiver usando o tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)) para auxiliar na configuração do ambiente, você pode começar a enviar dados de IOT simulados executando o projeto *DeviceSimulator* do exemplo. As instruções estão na seção [*configurar e executar a simulação*](tutorial-end-to-end.md#configure-and-run-the-simulation) do tutorial.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualizar os dados no Time Series Insights

Agora, os dados devem estar fluindo para sua instância de Time Series Insights, prontos para serem analisados. Siga as etapas abaixo para explorar os dados recebidos.

1. Abra seu ambiente de Time Series Insights no [portal do Azure](https://portal.azure.com) (você pode pesquisar o nome do seu ambiente na barra de pesquisa do Portal). Acesse a *URL do Gerenciador do Time Series Insights* mostrada na visão geral da instância.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Selecione a URL do Time Series Insights Explorer na guia Visão geral do ambiente de Time Series Insights":::

2. No Gerenciador, você verá os três gêmeos do Azure digital gêmeos mostrados à esquerda. Selecione _**thermostat67**_, selecione **temperatura** e clique em **Adicionar**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Selecione * * thermostat67 * *, selecione * * temperatura * * e pressione * * Adicionar * *":::

3. Agora você deve estar vendo as leituras de temperatura iniciais de seu termostato, conforme mostrado abaixo. A mesma leitura de temperatura é atualizada para *room21* e *FLOOR1*, e você pode visualizar esses fluxos de dados em tandem.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Os dados iniciais de temperatura estão grafos no Gerenciador de TSI. É uma linha de valores aleatórios entre 68 e 85":::

4. Se você permitir que a simulação seja executada por muito mais tempo, sua visualização terá uma aparência semelhante a esta:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Os dados de temperatura para cada linhas de entrelaçamento estão grafos em três linhas paralelas de cores diferentes.":::

## <a name="next-steps"></a>Próximas etapas

Os gêmeos digitais são armazenados por padrão como uma hierarquia simples no Time Series Insights, mas podem ser aprimorados com informações de modelo e uma hierarquia de vários níveis para a organização. Para saber mais sobre esse processo, leia: 

* [*Tutorial: definir e aplicar um modelo*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Você pode escrever uma lógica personalizada para fornecer automaticamente essas informações usando os dados do modelo e do grafo já armazenados no Azure digital gêmeos. Para ler mais sobre como gerenciar, atualizar e recuperar informações do grafo gêmeos, consulte as seguintes referências:

* [*Como gerenciar um teledigital*](./how-to-manage-twin.md)
* [*Como consultar o gráfico de entrelaçamento*](./how-to-query-graph.md)