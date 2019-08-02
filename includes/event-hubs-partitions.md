---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68481375"
---
Os Hubs de Evento fornecem streaming de mensagens por meio de um padrão de consumidor particionado no qual cada consumidor lê somente um subconjunto específico, ou partição, do fluxo de mensagens. Esse padrão permite a escala horizontal para processamento de eventos e fornece outros recursos centrados no fluxo que não estão disponíveis em filas e tópicos.

Uma partição é uma sequência ordenada de eventos que é mantida em um hub de eventos. À medida que novos eventos chegam, eles são adicionados ao final dessa sequência. Uma partição pode ser pensada como "log de confirmação".

![Hubs de Eventos](./media/event-hubs-partitions/partition.png)

Os Hubs de Eventos mantêm os dados por um período de retenção configurado que se aplica a todas as partições no hub de eventos. Eventos expiram periodicamente; não é possível excluí-los explicitamente. Como as partições são independentes e contêm sua própria sequência de dados, elas geralmente crescem a taxas diferentes.

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

O número de partições é especificado na criação do Hub de Eventos e deve estar entre 2 e 32. A contagem de partições não é mutável, portanto você deve considerar a escala de longo prazo ao definir a contagem de partições. As partições são um mecanismo de organização de dados relacionados ao paralelismo de downstream necessário no consumo de aplicativos. O número de partições em um hub de eventos está diretamente relacionado ao número de leitores simultâneos que você espera ter. Você pode aumentar o número de partições para mais de 32 entrando em contato com a equipe de Hubs de Eventos.

Talvez você queira defini-lo para ser o maior valor possível, que é 32, no momento da criação. Lembre-se de que ter mais de uma partição resultará em eventos enviados a várias partições sem reter o pedido, a menos que você configure os remetentes para enviar somente para uma única partição fora do 32 deixando as 31 restantes de partições redundantes. No primeiro caso, você terá que ler eventos em todas as partições 32. No último caso, não há nenhum custo adicional óbvio da configuração extra que você precisa fazer no host do processador de eventos.

Embora as partições possam ser identificadas e seja possível enviar diretamente a elas, não é recomendável enviar diretamente a uma partição. Em vez disso, você pode usar construções de nível superior introduzidas na seção [editores de eventos](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

As partições são preenchidas com uma sequência de dados de evento que contêm o corpo do evento, um recipiente de propriedades definidas pelo usuário e metadados como seu deslocamento na partição e seu número na sequência de fluxo.

É recomendável equilibrar unidades de produtividade 1:1 e partições para obter uma escala ideal. Uma única partição tem uma entrada garantida e saída de até uma unidade de taxa de transferência. Embora você possa obter uma taxa de transferência mais alta em uma partição, o desempenho não é garantido. É por isso que é altamente recomendável que o número de partições em um hub de eventos seja maior ou igual ao número de unidades de produtividade.

Considerando a taxa de transferência total que você planeja precisar, você sabe o número de unidades de produtividade que precisa e o número mínimo de partições, mas quantas partições você deve ter? Escolha o número de partições com base no paralelismo downstream que você deseja obter, bem como suas necessidades futuras de taxa de transferência. Não há nenhum custo para o número de partições que você tem em um hub de eventos.

Para saber mais sobre partições e a compensação entre disponibilidade e confiabilidade, consulte os artigos [Guia de programação dos Hubs de Eventos](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e [Disponibilidade e consistência em Hubs de Eventos](../articles/event-hubs/event-hubs-availability-and-consistency.md).
