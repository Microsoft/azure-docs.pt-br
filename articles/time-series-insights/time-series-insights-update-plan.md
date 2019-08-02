---
title: Planejar seu ambiente da Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Planeje o ambiente de versão prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 5f89105abc21f5ef6cce53ea55622a808f947e86
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357298"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planeje o ambiente de versão prévia do Azure Time Series Insights

Este artigo descreve as práticas recomendadas para planejar e começar a trabalhar rapidamente usando a visualização dos Insights de série de tempo do Azure.

> [!NOTE]
> Para obter práticas recomendadas planejar uma instância de séries temporais de disponibilidade geral, consulte [planejar seu ambiente de disponibilidade geral do Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planejamento e preparação

Para começar a usar o Time Series Insights, é bom entender:

* O que você obtém quando você [provisionar um ambiente de visualização de Insights de série de tempo](#the-preview-environment).
* O que seu [propriedades de IDs de série de tempo e o carimbo de hora são](#configure-time-series-ids-and-timestamp-properties).
* Nova [modelo de série temporal é](#understand-the-time-series-model)e como criar seus próprios.
* Como [enviar eventos com eficiência em JSON](#shape-your-events).
* Time Series Insights [opções de recuperação de desastres de negócios](#business-disaster-recovery).

Azure Time Series Insights emprega um modelo pago conforme o uso de negócios. Para saber mais sobre cobranças e capacidade, confira o [preço do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de visualização

Ao provisionar um ambiente de versão prévia do Time Series Insights, é possível criar dois recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights
* Uma conta de Uso Geral V1 do Armazenamento do Azure

Para começar, são necessários três itens adicionais:

* Um [modelo de série temporal](./time-series-insights-update-tsm.md)
* Uma [origem de evento conectada ao Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Os eventos fluem para a origem do evento](./time-series-insights-send-events.md) mapeada no modelo e válida em formato JSON

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar propriedades de IDs de série de tempo e carimbo de data

Para criar um novo ambiente do Time Series Insights, selecione uma ID de série temporal, que atuará como uma partição lógica dos dados. Conforme observado, deixe suas IDs de série temporal prontas.

> [!IMPORTANT]
> As IDs de série tempo são *imutáveis* e *não podem ser alteradas posteriormente*. Verifique cada uma antes da seleção final e do primeiro uso.

Você pode selecionar até três chaves para diferenciar exclusivamente seus recursos. Para saber mais, leia [Melhores práticas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md) e [Armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

A propriedade carimbo de data/hora também é importante. É possível designar essa propriedade ao adicionar origens de eventos. Cada origem de evento tem uma propriedade opcional de carimbo de data/hora usada para rastrear origens de eventos ao longo do tempo. Os valores de carimbo de data/hora diferenciam maiúsculas de minúsculas e precisam estar formatados de acordo com a especificação individual de cada origem de evento.

> [!TIP]
> Verifique os requisitos de formatação e análise das origens de evento.

Quando deixado em branco, o Tempo de Enfileiramento do Evento de uma origem do evento é usado como o carimbo de data/hora do evento. Se você enviar dados históricos ou eventos em lote, personalizar a propriedade carimbo de data/hora será mais útil que o padrão Tempo de Enfileiramento do Evento. Para obter mais informações, leia sobre como [Adicionar origens de eventos no IoT Hub do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Entenda o modelo de série temporal

Agora, é possível configurar o modelo de série temporal do ambiente do Time Series Insights. O novo modelo facilita localizar e analisar dados IoT. Permite a preservação, a manutenção e o enriquecimento dos dados de série temporal e ajuda a preparar conjuntos de dados prontos para consumo. O modelo usa as IDs de série de tempo, que são mapeados para uma instância que associa o recurso exclusivo com variáveis, conhecidas como tipos e hierarquias. Leia sobre o novo [modelo de série temporal](./time-series-insights-update-tsm.md).

O modelo é dinâmico, portanto, pode ser criado a qualquer momento. Para começar rapidamente, crie e envie-o por upload antes de efetuar push dos dados para o Time Series Insights. Para criar seu modelo, confira [Usar o modelo de série temporal](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série temporal mapeia um modelo existente do ativo ou sistema ERP já em vigor. Se você não tiver um modelo existente, uma experiência de usuário predefinida será [fornecida](https://github.com/Microsoft/tsiclient) para começar rapidamente. Para prever como um modelo poderá ajudar você, exiba o [exemplo de ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Formatar os eventos

É possível verificar a maneira como você envia eventos para o Time Series Insights. O ideal é que os eventos sejam desnormalizados de maneira eficiente.

Uma boa regra prática:

* Store metadados em seu modelo de série temporal.
* Modo de série de tempo, os campos de instância e eventos incluem informações somente é necessário, como uma ID de série de tempo ou carimbo de hora.

Para saber mais, confira [Formatar eventos](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Revisão [Azure Advisor](../advisor/advisor-overview.md) planejar suas opções de configuração de recuperação de negócios.

- Leia mais sobre [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na visualização de Insights de série do tempo.

- Saiba mais sobre [modelagem de dados](./time-series-insights-update-tsm.md) na visualização de Insights de série do tempo.