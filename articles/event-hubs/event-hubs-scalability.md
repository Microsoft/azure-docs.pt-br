---
title: Escalabilidade, os Hubs de eventos do Azure | Microsoft Docs
description: Fornece informações sobre como dimensionar os Hubs de eventos do Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: c46b333f2cc304cc12ddf78670b60940c7bc0db3
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827704"
---
# <a name="scaling-with-event-hubs"></a>Dimensionamento com os Hubs de eventos

Há dois fatores que influenciam o dimensionamento com os Hubs de eventos.
*   Unidades de transferência
*   Partições

## <a name="throughput-units"></a>Unidades de transferência

A capacidade de transferência dos Hubs de Eventos é controlada pelas *unidades de transferência*. As unidades de taxa de transferência são unidades de capacidade pré-adquiridas. Uma única taxa de transferência permite que você:

* Entrada: Até 1 MB por segundo ou 1000 eventos por segundo (o que ocorrer primeiro).
* Saída: Até 2 MB por segundo ou 4096 eventos por segundo.

Além da capacidade de unidades de transferência adquiridas, a entrada está limitada e uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) é retornada. A saída não gera exceções de limitação, mas ainda é limitada à capacidade das unidades de produtividade adquiridas. Se você receber exceções de taxa de publicação ou estiver esperando ver mais saída, verifique quantas unidades de transferência você comprou para o namespace. Você pode gerenciar unidades de produtividade na folha **Escala** dos namespaces no [portal do Azure](https://portal.azure.com). Você também pode gerenciar unidades de produtividade programaticamente usando as [APIs de Hubs de Eventos](event-hubs-api-overview.md).

As unidades de taxa de transferência são pré-adquiridas e cobradas por hora. Depois de adquiridas, as unidades de taxa de transferência são cobradas por um mínimo de uma hora. Até 20 unidades de produtividade podem ser adquiridas para um namespace de Hubs de Eventos e elas são compartilhadas entre todos os Hubs de Eventos no namespace.

O recurso **inflar automaticamente** dos Hubs de Eventos escala verticalmente automaticamente aumentando o número de unidades de taxa de transferência para atender às necessidades de uso. O aumento de unidades de taxa de transferência evita cenários de limitação, nos quais:

- As taxas de entrada de dados excedem as unidades de taxa de transferência definidas.
- As taxas de solicitação de saída de dados excedem as unidades de taxa de transferência definidas.

O serviço de Hubs de Eventos aumenta a taxa de transferência quando a carga aumentar ultrapassando o limite mínimo, sem quaisquer solicitações com falha com erros de ServerBusy. 

Para obter mais informações sobre o recurso inflar, consulte [dimensionar automaticamente as unidades de produtividade](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partições
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Chave de partição

Use uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de evento de entrada em partições específicas para fins de organização de dados. A chave de partição é um valor fornecido pelo remetente passado para um hub de eventos. Ela é processada por meio de uma função de hash estática, que cria a atribuição de partição. Se você não especificar uma chave de partição ao publicar um evento, uma atribuição de round robin será usada.

O editor de eventos só está ciente da sua chave de partição, não da partição para a qual os eventos são publicados. Essa desassociação de chave e partição isenta o remetente da necessidade de saber muito sobre o processamento de downstream. Uma identidade por dispositivo ou exclusiva do usuário é uma boa chave de partição, mas outros atributos, como geografia, também podem ser usados para agrupar eventos relacionados em uma única partição.


## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

- [Dimensionar automaticamente as unidades de taxa de transferência](event-hubs-auto-inflate.md)
- [Visão geral do serviço dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
