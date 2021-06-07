---
title: Escalabilidade-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como dimensionar os hubs de eventos do Azure usando partições e unidades de produtividade.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601264"
---
# <a name="scaling-with-event-hubs"></a>Dimensionamento com hubs de eventos

Há dois fatores que influenciam o dimensionamento com os hubs de eventos.
*   Unidades de transferência
*   Partições

## <a name="throughput-units"></a>Unidades de transferência

A capacidade de taxa de transferência dos hubs de eventos é controlada por *unidades de produtividade*. As unidades de taxa de transferência são unidades de capacidade pré-adquiridas. Uma única taxa de transferência permite que você:

* Entrada: até 1 MB por segundo ou 1000 eventos por segundo (o que ocorrer primeiro).
* Entrada: até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade de unidades de transferência adquiridas, a entrada está limitada e uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) é retornada. A saída não gera exceções de limitação, mas ainda é limitada à capacidade das unidades de produtividade adquiridas. Se você receber exceções de taxa de publicação ou estiver esperando ver mais saída, verifique quantas unidades de transferência você comprou para o namespace. Você pode gerenciar unidades de produtividade na folha **Escala** dos namespaces no [portal do Azure](https://portal.azure.com). Você também pode gerenciar unidades de produtividade programaticamente usando as [APIs de Hubs de Eventos](./event-hubs-samples.md).

As unidades de taxa de transferência são pré-adquiridas e cobradas por hora. Depois de adquiridas, as unidades de taxa de transferência são cobradas por um mínimo de uma hora. Até 20 unidades de produtividade podem ser adquiridas para um namespace de Hubs de Eventos e elas são compartilhadas entre todos os Hubs de Eventos no namespace.

O recurso **inflar automaticamente** dos Hubs de Eventos escala verticalmente automaticamente aumentando o número de unidades de taxa de transferência para atender às necessidades de uso. O aumento de unidades de taxa de transferência evita cenários de limitação, nos quais:

- As taxas de entrada de dados excedem as unidades de taxa de transferência definidas.
- As taxas de solicitação de saída de dados excedem as unidades de taxa de transferência definidas.

O serviço de Hubs de Eventos aumenta a taxa de transferência quando a carga aumentar ultrapassando o limite mínimo, sem quaisquer solicitações com falha com erros de ServerBusy. 

Para obter mais informações sobre o recurso de inflar automaticamente, consulte [dimensionar automaticamente unidades de produtividade](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

- [Dimensionar automaticamente unidades de transferência](event-hubs-auto-inflate.md)
- [Visão geral do serviço dos Hubs de Eventos](./event-hubs-about.md)
