---
title: Planejar seu ambiente Gen2-Azure Time Series Insights | Microsoft Docs
description: Práticas recomendadas para configurar, gerenciar, planejar e implantar seu ambiente de Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016778"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Planejar o ambiente do Azure Time Series Insights Gen2

Este artigo descreve as práticas recomendadas para planejar e começar rapidamente usando o Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planejamento e preparação

As práticas recomendadas sobre o planejamento e a preparação de seu ambiente são descritas mais detalhadamente nos seguintes artigos:

* O que você obtém ao [provisionar um ambiente de Azure Time Series insights Gen2](#the-gen2-environment).
* Quais [são suas IDs de série temporal e propriedades de carimbo de data/hora](#configure-time-series-ids-and-timestamp-properties).
* Qual é o novo [modelo de série temporal](#understand-the-time-series-model)e como criar os seus próprios.
* Como [enviar eventos com eficiência no JSON](#shape-your-events).
* Azure Time Series Insights [Opções de recuperação de desastres de negócios](#business-disaster-recovery).

Azure Time Series Insights emprega um modelo de negócios pago conforme o uso. Para obter mais informações sobre cobranças e capacidade, leia [Azure Time Series insights preços](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>O ambiente Gen2

Ao provisionar um ambiente de Azure Time Series Insights Gen2, você cria dois recursos do Azure:

* Um ambiente de Azure Time Series Insights Gen2
* Uma conta de armazenamento do Azure

Como parte do processo de provisionamento, você especifica se deseja habilitar uma loja a quente. A loja a quente fornece uma experiência de consulta em camadas. Quando habilitado, você deve especificar um período de retenção entre 7 e 30 dias. As consultas executadas no período de retenção de armazenamento quente geralmente fornecem tempos de resposta mais rápidos. Quando uma consulta se estende pelo período de retenção de armazenamento quente, ela é servida por meio da loja fria.

As consultas na loja a quente são gratuitas, enquanto as consultas em armazenamento frio incorrem em custos. É importante entender seus padrões de consulta e planejar sua configuração de armazenamento quente adequadamente. É recomendável que a análise interativa nos dados mais recentes resida em sua loja e análise de padrões quentes e tendências de longo prazo residam em frio.

> [!NOTE]
> Para ler mais sobre como consultar seus dados quentes, leia a [referência da API](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters).

Para começar, são necessários três itens adicionais:

* Um [modelo de série temporal](./concepts-model-overview.md)
* Uma [origem de evento conectada ao Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* [Os eventos fluem para a origem do evento](./time-series-insights-send-events.md) mapeada no modelo e válida em formato JSON

## <a name="review-azure-time-series-insights-gen2-limits"></a>Examinar Azure Time Series Insights limites de Gen2

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar IDs de série temporal e propriedades de carimbo de data/hora

Para criar um novo ambiente de Azure Time Series Insights, selecione uma ID de série temporal. que atuará como uma partição lógica dos dados. Conforme observado, deixe suas IDs de série temporal prontas.

> [!IMPORTANT]
> As IDs de série temporal *não podem ser alteradas posteriormente*. Verifique cada uma antes da seleção final e do primeiro uso.

Você pode selecionar até três chaves para diferenciar exclusivamente seus recursos. Para obter mais informações, leia [as práticas recomendadas para escolher uma ID de série temporal](./how-to-select-tsid.md) e [regras de ingestão](concepts-json-flattening-escaping-rules.md).

A propriedade **timestamp** também é importante. É possível designar essa propriedade ao adicionar origens de eventos. Cada origem de evento tem uma propriedade opcional de carimbo de data/hora usada para rastrear origens de eventos ao longo do tempo. Os valores de carimbo de data/hora diferenciam maiúsculas de minúsculas e precisam estar formatados de acordo com a especificação individual de cada origem de evento.

Quando deixado em branco, a hora em que o evento foi enfileirado no Hub IoT ou no Hub de eventos é usada como o carimbo de data/hora do evento. Em geral, os usuários devem optar por personalizar a propriedade Timestamp e usar a hora em que o sensor ou a marca gerou a leitura, em vez do tempo enfileirado do Hub. Para obter mais informações e ler sobre deslocamentos de fuso horário, leia [carimbo de data/hora de origem do evento](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp).

## <a name="understand-the-time-series-model"></a>Entenda o modelo de série temporal

Agora você pode configurar o modelo de série temporal do ambiente de Azure Time Series Insights. O novo modelo facilita localizar e analisar dados IoT. Permite a preservação, a manutenção e o enriquecimento dos dados de série temporal e ajuda a preparar conjuntos de dados prontos para consumo. O modelo usa IDs de série temporal, que mapeiam para uma instância que associa o recurso exclusivo com variáveis, conhecidas como tipos e hierarquias. Leia sobre a visão geral do [modelo de série temporal](./concepts-model-overview.md) para saber mais.

O modelo é dinâmico, portanto, pode ser criado a qualquer momento. Para começar rapidamente, crie e carregue-o antes de enviar dados por push para Azure Time Series Insights. Para criar seu modelo, leia [usar o modelo de série temporal](./concepts-model-overview.md).

Para muitos clientes, o modelo de série temporal mapeia um modelo existente do ativo ou sistema ERP já em vigor. Se você não tiver um modelo existente, uma experiência de usuário predefinida será [fornecida](https://github.com/Microsoft/tsiclient) para começar rapidamente. Para prever como um modelo poderá ajudar você, exiba o [exemplo de ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Formatar os eventos

Você pode verificar a maneira como envia eventos para Azure Time Series Insights. O ideal é que os eventos sejam desnormalizados de maneira eficiente.

Uma boa regra prática:

* Armazene metadados no seu modelo de série temporal.
* Verifique se o modo de série temporal, os campos de instância e os eventos incluem apenas as informações necessárias, como uma ID de série temporal ou uma propriedade Timestamp.

Para obter mais informações e entender como os eventos serão mesclados e armazenados, leia as [regras de escape e mesclagem JSON](./concepts-json-flattening-escaping-rules.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

* Examine o [Azure Advisor](../advisor/advisor-overview.md) para planejar suas opções de configuração de recuperação de negócios.
* Examine o [Azure Advisor](../advisor/advisor-overview.md) para planejar suas opções de configuração de recuperação de negócios.
* Leia mais sobre a [ingestão de dados](./concepts-ingestion-overview.md) no Azure Time Series insights Gen2.
* Examine o artigo sobre [armazenamento de dados](./concepts-storage.md) em Azure Time Series insights Gen2.
* Saiba mais sobre a [modelagem de dados](./concepts-model-overview.md) no Azure Time Series insights Gen2.