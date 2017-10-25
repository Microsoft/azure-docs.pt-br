---
title: Diagnosticar e resolver problemas | Microsoft Docs
description: Este tutorial aborda como diagnosticar e resolver problemas no ambiente do Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.openlocfilehash: 4e10a009eb67706d927ece5692134d802094cdf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas no ambiente do Time Series Insights

## <a name="i-dont-see-my-data"></a>Não vejo meus dados
Estes são alguns motivos pelos quais talvez você não consiga ver seus dados no ambiente do [Portal do Azure Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>A origem do evento não tem dados no formato JSON
Atualmente, o Azure Time Series Insights dá suporte apenas a dados JSON. Para obter exemplos do JSON, consulte [Formas de JSON com suporte](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>Ao registrar a origem do evento, você não forneceu a chave que tem a permissão necessária
* Para o Hub IoT, você precisa fornecer a chave com a permissão de **conexão de serviço**.

   ![Permissão de conexão de serviço do Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Conforme mostrado na imagem anterior, as políticas **iothubowner** e **service** funcionarão, pois ambas têm a permissão de **conexão de serviço**.
* Para o hub de eventos, você precisa fornecer a chave que tem a permissão de **Escuta**.

   ![Permissão de escuta do hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Conforme mostrado na imagem anterior, qualquer uma das políticas **read** e **manage** funcionará, pois ambas têm a permissão de **Escuta**.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>O grupo de consumidores fornecido não é exclusivo para o Time Series Insights
Para o Hub IoT ou o hub de eventos, durante o registro, solicitamos que você especifique o grupo de consumidores que deve ser usado para a leitura dos dados. Esse grupo de consumidores não deve ser compartilhado. Se ele for compartilhado, o hub de eventos subjacente desconectará automaticamente um dos leitores de forma aleatória.

## <a name="i-see-my-data-but-theres-a-lag"></a>Consigo ver meus dados, mas há um retardo
Estes são motivos pelos quais você pode ver dados parciais no ambiente do [Portal do Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>O ambiente está ficando restrito
A limitação é imposta com base na capacidade e no tipo de SKU do ambiente. Todas as origens do evento no ambiente compartilham essa capacidade. Se a origem do evento do hub de eventos ou Hub IoT estiver enviando dados por push além dos limites impostos, você vê a limitação e um retardo.

O diagrama a seguir mostra um ambiente do Time Series Insights com um SKU S1 e uma capacidade 3. Ele pode ingressar 3 milhões de eventos por dia.

![Capacidade atual do SKU do ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Suponha que esse ambiente está ingerindo mensagens de um hub de eventos com uma taxa de entrada mostrada no seguinte diagrama:

![Taxa de entrada de exemplo para um hub de eventos](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Conforme mostrado no diagrama, a taxa de entrada diária é de ~67.000 mensagens. Essa taxa converte aproximadamente 46 mensagens a cada minuto. Se cada mensagem do hub de eventos for nivelada para um único evento do Time Series Insights, esse ambiente não terá nenhuma limitação. Se cada mensagem do hub de eventos for nivelada para 100 eventos do Time Series Insights, 4.600 eventos deverão ser ingeridos a cada minuto. Um ambiente de SKU S1 que tem uma capacidade 3 pode ingressar apenas 2.100 eventos por minuto (1 milhão de eventos por dia = 700 eventos por minuto em 3 unidades = 2.100 eventos por minuto). Portanto, você observa um retardo devido à limitação. 

Para obter um entendimento de alto nível sobre como funciona a lógica de nivelamento, consulte a seção [Formas de JSON com suporte](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Etapas recomendadas
Para corrigir o retardo, aumente a capacidade do SKU do ambiente. Para obter mais informações, consulte [Como dimensionar o ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Você está enviando dados históricos e causando a entrada lenta
Se você estiver se conectando a uma origem do evento existente, é provável que o Hub IoT ou o hub de eventos já contenha dados. Portanto, o ambiente inicia o pull dos dados desde o início do período de retenção de mensagens da origem do evento. 

Esse comportamento é o comportamento padrão e não pode ser substituído. Você pode acionar a limitação e pode levar alguns instantes para a atualização da ingestão de dados históricos.

#### <a name="recommended-steps"></a>Etapas recomendadas
Para corrigir o retardo, execute as seguintes etapas:
1. Aumente a capacidade do SKU para o valor máximo permitido (10, neste caso). Após o aumento da capacidade, o processo de entrada começa a ser atualizado muito mais rapidamente. É possível visualizar a rapidez da atualização por meio do gráfico de disponibilidade no [Portal do Time Series Insights](https://insights.timeseries.azure.com). Você é cobrado pelo aumento da capacidade.
2. Depois que o retardo for atualizado, diminua a capacidade do SKU novamente para a taxa de entrada normal.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>A configuração *nome da propriedade do carimbo de data/hora* de minha origem do evento não funciona
Verifique se o nome e o valor estão em conformidade com as seguintes regras:
* O nome da propriedade do carimbo de data/hora _diferencia maiúsculas de minúsculas_.
* O valor da propriedade do carimbo de data/hora obtido da origem do evento, como uma cadeia de caracteres JSON, deve ter o formato _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_. Um exemplo de uma cadeia de caracteres desse tipo é “2008-04-12T12:53Z”.
