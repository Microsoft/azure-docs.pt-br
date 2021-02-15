---
title: Enviar eventos para um ambiente-Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar um hub de eventos, executar um aplicativo de exemplo e enviar eventos para seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 9a9115b5400cc6d6c1ecc5740af796d831f5dee3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023251"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>Enviar eventos para um ambiente de Azure Time Series Insights Gen1 usando um hub de eventos

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo explica como criar e configurar um hub de eventos nos hubs de eventos do Azure. Ele também descreve como executar um aplicativo de exemplo para enviar eventos por push a Azure Time Series Insights dos hubs de eventos. Se você tiver um hub de eventos existente com eventos no formato JSON, pule este tutorial e exiba seu ambiente em [Azure Time Series insights](./tutorials-set-up-tsi-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para saber como criar um hub de eventos, leia a [documentação dos hubs de eventos](../event-hubs/index.yml).
1. Na caixa de pesquisa, pesquise **Hubs de Eventos**. Na lista retornada, selecione **Hubs de Eventos**.
1. Selecione seu hub de eventos.
1. Ao criar um hub de eventos, você está criando um namespace de Hub de eventos. Se você ainda não criou um hub de eventos dentro do namespace, no menu, em **entidades**, crie um hub de eventos.  

    [![Lista de hubs de eventos](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Depois de criar um hub de eventos, selecione-o na lista de hubs de eventos.
1. No menu, em **entidades**, selecione **hubs de eventos**.
1. Selecione o nome do hub de eventos para configurá-lo.
1. Em **visão geral**, selecione **grupos de consumidores** e, em seguida, selecione grupo de **consumidores**.

    [![Criar um grupo de consumidores](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Certifique-se de criar um grupo de consumidores que é usado exclusivamente pelo seu Azure Time Series Insights origem do evento.

    > [!IMPORTANT]
    > Verifique se esse grupo de consumidores não é usado por nenhum outro serviço, como um trabalho Azure Stream Analytics ou outro ambiente de Azure Time Series Insights. Se o grupo de consumidores é usado por outros serviços, as operações de leitura são prejudicadas em relação a esse ambiente e a outros serviços. Se você usar **$Default** como o grupo de consumidores, outros leitores poderão vir a reutilizar seu grupo de consumidores.

1. No menu, em **configurações**, selecione **políticas de acesso compartilhado** e, em seguida, selecione **Adicionar**.

    [![Selecionar Políticas de acesso compartilhado e selecione o botão Adicionar](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. No painel **Adicionar nova política de acesso compartilhado**, crie um acesso compartilhado denominado **MySendPolicy**. Você usa essa política de acesso compartilhado para enviar eventos nos exemplos do C# posteriormente neste artigo.

    [![Na caixa Nome de política, insira MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Em **declaração**, marque a caixa de seleção **Enviar** .

## <a name="add-an-azure-time-series-insights-instance"></a>Adicionar uma instância de Azure Time Series Insights

No Azure Time Series Insights Gen2, você pode adicionar dados contextuais à telemetria de entrada usando o modelo de série temporal (TSM). No TSM, suas marcas ou sinais são referidos como *instâncias* e você pode armazenar dados contextuais em *campos de instância.* Os dados são unidos no momento da consulta usando uma **ID da série temporal**. A **ID da série temporal** para o projeto Windmills de exemplo que usamos mais adiante neste artigo é `id` . Para saber mais sobre como armazenar dados em campos de instância, leia a visão geral do [modelo de série temporal](./concepts-model-overview.md) .

### <a name="create-an-azure-time-series-insights-event-source"></a>Criar uma origem de evento de Azure Time Series Insights

1. Se você ainda não criou uma origem de evento, conclua as etapas para [criar uma origem de evento](./how-to-ingest-data-event-hub.md).

1. Defina um valor para `timeSeriesId`. Para saber mais sobre a **ID da série temporal**, leia [modelos de série temporal](./concepts-model-overview.md).

### <a name="push-events-to-windmills-sample"></a>Enviar eventos por push para o exemplo de Windmills

1. Na barra de pesquisa, procure **Hubs de Eventos**. Na lista retornada, selecione **Hubs de Eventos**.

1. Selecione sua instância do hub de eventos.

1. Vá para **políticas de acesso compartilhado**  >  **MySendPolicy**. Copie o valor da **cadeia de conexão-chave primária**.

    [![Copiar o valor da cadeia de conexão da chave primária](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Acesse <https://tsiclientsample.azurewebsites.net/windFarmGen.html>. A URL cria e executa dispositivos simulados do Windmill.
1. Na caixa **cadeia de conexão do hub de eventos** na página da Web, Cole a cadeia de conexão que você copiou no [campo de entrada Windmill](#push-events-to-windmills-sample).
  
    [![Colar a cadeia de conexão da chave primária na caixa Cadeia de Conexão do Hub de Eventos](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selecione **Clique para iniciar**.

    > [!TIP]
    > O simulador do Windmill também cria JSON que você pode usar como uma carga com as [APIs de consulta do Azure Time Series insights GA](/rest/api/time-series-insights/gen1-query).

    > [!NOTE]
    > O simulador continuará a enviar dados até que a guia navegador seja fechada.

1. Volte ao seu hub de eventos no portal do Azure. Na página **visão geral** , os novos eventos recebidos pelo hub de eventos são exibidos.

    [![Uma página de visão geral do hub de eventos que mostra as métricas do hub de eventos](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Formas de JSON com suporte

### <a name="example-one"></a>Exemplo um

* **Entrada**: um objeto JSON simples.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Saída**: um evento.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exemplo dois

* **Input**: uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido em um evento.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Saída**: dois eventos.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exemplo três

* **Input**: um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Saída**: dois eventos. A propriedade **location** é copiada para cada evento.

    |local|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |Oeste dos EUA|device1|2016-01-08T01:08:00Z|
    |Oeste dos EUA|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exemplo quatro

* **Input**: um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que as propriedades globais podem ser representadas pelo objeto JSON complexo.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Saída**: dois eventos.

    |local|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |Oeste dos EUA|manufacturer1|Leste dos EUA|device1|2016-01-08T01:08:00Z|pressão|psi|108.09|
    |Oeste dos EUA|manufacturer1|Leste dos EUA|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Próximas etapas

* [Exiba seu ambiente](https://insights.timeseries.azure.com) no Azure Time Series insights Explorer.

* Leia mais sobre [as mensagens do dispositivo do Hub IOT](../iot-hub/iot-hub-devguide-messages-construct.md)