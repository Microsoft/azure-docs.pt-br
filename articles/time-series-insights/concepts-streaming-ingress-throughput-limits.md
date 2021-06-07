---
title: Limitações de taxa de transferência de ingestão de streaming-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre limites de taxa de transferência de entrada em Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 72b78f49ed8b7016b92262de7256492fa6d5d7d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676794"
---
# <a name="streaming-ingestion-throughput-limits"></a>Limites de taxa de transferência de ingestão de streaming

Azure Time Series Insights as limitações de entrada de dados de streaming Gen2 são descritas abaixo.

> [!TIP]
> Leia [planejar seu ambiente de Azure Time Series insights Gen2](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) para obter uma lista abrangente de todos os limites.

## <a name="per-environment-limitations"></a>Limitações por ambiente

Em geral, as taxas de entrada são exibidas como o fator do número de dispositivos que estão em sua organização, a frequência de emissão de eventos e o tamanho de cada evento:

* **Número de dispositivos** × **Frequência de emissão de eventos** × **Tamanho de cada evento**.

Por padrão, Azure Time Series Insights Gen2 pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (Mbps) por Azure Time Series insights ambiente do Gen2**. Há limitações adicionais [por partição de hub](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * O suporte de ambiente para velocidades de ingestão de até 2 MBps pode ser fornecido por solicitação.
> * Entre em contato conosco se você precisar de uma taxa de transferência maior enviando um tíquete de suporte por meio do portal do Azure.

* **Exemplo 1:**

    O envio da Contoso tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Eles estão usando um hub IoT com quatro partições como a origem do evento Azure Time Series Insights Gen2.

  * A taxa de ingestão para seu ambiente de Azure Time Series Insights Gen2 seria: **100.000 dispositivos * 200 bytes/evento * (3/60 evento/s) = 1 Mbps**.
    * Supondo partições balanceadas, a taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão da Contoso Shipping estaria dentro das limitações de escala.

* **Exemplo 2:**

    A análise de frota da Contoso tem 10.000 dispositivos que emitem um evento a cada segundo. Eles estão usando um hub de eventos com uma contagem de partições igual a 2 como a origem do evento Azure Time Series Insights Gen2. O tamanho de um evento é de 200 bytes.

  * A taxa de ingestão de ambiente seria: **10.000 dispositivos * 200 bytes/evento * 1 evento/s = 2 Mbps**.
    * Supondo partições balanceadas, sua taxa por partição seria de 1 MBps.
    * A taxa de ingestão da Análise de Frota da Contoso está acima dos limites do ambiente e da partição. Eles podem enviar uma solicitação para Azure Time Series Insights Gen2 por meio do portal do Azure para aumentar a taxa de ingestão para seu ambiente e criar um hub de eventos com mais partições dentro dos limites.

## <a name="hub-partitions-and-per-partition-limits"></a>Partições de Hub e limites por partição

Ao planejar o ambiente do Azure Time Series Insights Gen2, é importante considerar a configuração das origens de evento que você conectará ao Azure Time Series Insights Gen2. O Hub IoT do Azure e os Hubs de Eventos utilizam partições para habilitar a escala horizontal para processamento de eventos.

Uma *partição* é uma sequência ordenada de eventos mantidos em um hub. A contagem de partições é definida durante a fase de criação do hub e não pode ser alterada.

Para consultar as melhores práticas de particionamento de Hubs de Eventos, analise [Quantas partições eu preciso?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

> [!NOTE]
> A maioria dos hubs IoT usados com Azure Time Series Insights Gen2 só precisa de quatro partições.

Se você estiver criando um novo hub para seu ambiente de Azure Time Series Insights Gen2 ou usando um existente, precisará calcular a taxa de ingestão por partição para determinar se ela está dentro dos limites.

Azure Time Series Insights Gen2 atualmente tem um **limite geral por partição de 0,5 Mbps**.

### <a name="iot-hub-specific-considerations"></a>Considerações específicas do Hub IoT

Quando um dispositivo é criado no Hub IoT, ele é atribuído permanentemente a uma partição. Ao fazer isso, o Hub IoT é capaz de garantir a ordenação de eventos (já que a atribuição nunca é alterada).

Uma atribuição de partição fixa também afeta Azure Time Series Insights instâncias Gen2 que estão ingerindo dados enviados do Hub IoT downstream. Quando as mensagens de vários dispositivos são encaminhadas para o Hub usando a mesma ID de dispositivo de gateway, elas podem chegar na mesma partição ao mesmo tempo, potencialmente excedendo os limites de escala por partição.

**Impacto**:

* Se uma única partição apresentar uma taxa sustentada de ingestão sobre o limite, é possível que Azure Time Series Insights Gen2 não sincronize toda a telemetria do dispositivo antes que o período de retenção de dados do Hub IoT seja excedido. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem excedidos de modo consistente.

Para atenuar essa situação, recomendamos as seguintes melhores práticas:

* Calcule suas taxas de ingestão por ambiente e por partição antes de implantar sua solução.
* Verifique se os dispositivos do Hub IoT têm balanceamento de carga para a extensão mais distante possível.

> [!IMPORTANT]
> Para ambientes que usam o Hub IoT como uma origem de evento, calcule a taxa de ingestão usando o número de dispositivos de Hub em uso para ter certeza de que a taxa está abaixo da limitação de 0,5 MBps por partição.
>
> * Mesmo que vários eventos cheguem simultaneamente, o limite não será excedido.

  ![Diagrama de partição do Hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre como otimizar a taxa de transferência e as partições do hub:

* [Escala do Hub IoT](../iot-hub/iot-hub-scaling.md)
* [Escala do Hub de Eventos](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Partições do Hub de Eventos](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Próximas etapas

* Leia sobre o [armazenamento](./concepts-storage.md) de dados
