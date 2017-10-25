---
title: "Preços e cobrança do Barramento de Serviço | Microsoft Docs"
description: "Visão geral da estrutura de preços de Barramento de Serviço."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 8f693bc51fc9635fae4376137e7e573bf74da7cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-pricing-and-billing"></a>Barramento de Serviço, preços e cobrança
O Barramento de Serviço é oferecido nas camadas Standard e [Premium](service-bus-premium-messaging.md). É possível escolher uma camada de serviço para cada namespace de serviço do Barramento de Serviço criado por você, e essa seleção de camada aplica-se a todas as entidades criadas dentro desse namespace.

> [!NOTE]
> Para obter informações detalhadas sobre os preços atuais do Barramento de Serviço, consulte a [Azure Service Bus pricing page (Página de preços do Barramento de Serviço do Azure)](https://azure.microsoft.com/pricing/details/service-bus/) e [Perguntas frequentes sobre o Barramento de Serviço](service-bus-faq.md#pricing).
>
>

O Barramento de Serviço usa os dois seguintes medidores para filas e tópicos/assinaturas:

1. **Operações de sistema de mensagens**: definido como chamadas à API em pontos de extremidade de serviço de fila ou tópico/assinatura. Esse medidor substitui as mensagens enviadas ou recebidas como a unidade principal de uso cobrável para filas e tópicos/assinaturas.
2. **Conexões agenciadas**: definidas como o número máximo de conexões persistentes abertas em filas, tópicos ou assinaturas durante um determinado período de amostragem de uma hora. Esse medidor se aplica somente à camada Standard, na qual você pode abrir conexões adicionais (anteriormente, as conexões eram limitadas a 100 por fila/tópico/assinatura) a uma taxa nominal por conexão.

A camada **Standard** apresenta preços graduados para operações executadas com filas e tópicos/assinaturas, resultando em descontos por volume de até 80% altos níveis de uso. Há também um encargo de base da camada Standard de US $10 por mês, que permite que você execute até 12,5 milhões de operações por mês sem custos adicionais.

A camada **Premium** fornece isolamento de recursos na camada de CPU e memória, de modo que a carga de trabalho do cliente seja executada isoladamente. Esse contêiner de recurso é chamado de *unidade do sistema de mensagens*. Cada namespace premium é alocado para pelo menos uma unidade do sistema de mensagens. Você pode adquirir 1, 2 ou 4 unidades do sistema de mensagens para cada namespace Premium do Barramento de serviço. Uma única carga de trabalho ou entidade pode abranger várias unidades do sistema de mensagens, e o número de unidades do sistema de mensagens pode ser alterado à vontade, embora a cobrança seja feita por taxas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível para sua solução baseada no Barramento de Serviço. Esse desempenho não é apenas o mais previsível e disponível, mas também o mais rápido.

Observe que o custo base da faixa Standard é cobrado apenas uma vez por mês por assinatura do Azure. Isso significa que depois de criar um namespace individual do Barramento de Serviço na camada Standard, você poderá criar quantos namespaces Standard adicionais desejar na mesma assinatura do Azure, sem incorrer em encargos base adicionais.

A tabela [Preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/) resume as diferenças funcionais entre as camadas Standard e Premium.

## <a name="messaging-operations"></a>Operações de sistema de mensagens
Como parte do novo modelo de preço, a cobrança para filas e tópicos/assinaturas está mudando. Essas entidades estão fazendo a transição de cobrança por mensagem para cobrança por operação. Uma “operação” se refere a qualquer chamada à API feita em relação a um ponto de extremidade de serviço de fila ou tópico/assinatura. Isso inclui operações de gerenciamento, envio/recebimento e estado da sessão.

| Tipo de operação | Descrição |
| --- | --- |
| Gerenciamento |Criar, ler, atualizar, excluir (CRUD) em relação a filas ou tópicos/assinaturas. |
| Mensagens |Enviando e recebendo mensagens com filas ou tópicos/assinaturas. |
| Estado de sessão |Obter ou definir o estado de sessão em uma fila ou tópico/assinatura. |

Para obter detalhes de custo, consulte os preços listados na página [Preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="brokered-connections"></a>Conexões orientadas
*Conexões agenciadas* acomodam padrões de uso do cliente que envolvem um grande número de remetentes/destinatários “persistentemente conectados” em filas, tópicos ou assinaturas. Remetentes/destinatários persistentemente conectados são aqueles que se conectam com AMQP ou HTTP diferente com tempo limite de recebimento diferente de zero (por exemplo, sondagem longa de HTTP). Remetentes e receptores HTTP com um tempo limite imediato não geram conexões orientadas.

Para cotas de conexão e outros limites de serviço, consulte o artigo [Cotas do Barramento de Serviço](service-bus-quotas.md). Para obter mais informações sobre conexões agenciadas, consulte a seção [Perguntas frequentes](#faq) mais adiante neste tópico.

A camada Standard remove o limite de conexão agenciada por namespace e conta o uso total de conexão orientada na assinatura do Azure. Para obter mais informações, consulte a tabela [Conexões orientadas](https://azure.microsoft.com/pricing/details/service-bus/).

> [!NOTE]
> A camada do sistema de mensagens Standard inclui 1.000 conexões agenciadas (no custo base) que podem ser compartilhados entre todas as filas, tópicos e assinaturas com a assinatura do Azure associada.
>
>

<br />

> [!NOTE]
> A cobrança baseia-se no número máximo de conexões simultâneas e é rateada por hora com base em 744 horas por mês.
>
>

### <a name="premium-tier"></a>Camada Premium

As conexões orientadas não são cobradas na camada Premium.

## <a name="faq"></a>Perguntas frequentes

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>O que são conexões orientadas e como serei cobrado para elas?
Uma conexão orientada é definida como uma das conexões a seguir:

1. Uma conexão AMQP de um cliente com uma fila ou tópico/assinatura do Barramento de Serviço.
2. Uma chamada HTTP para receber uma mensagem de uma fila ou tópico do Barramento de Serviço que possua um valor de tempo limite de recebimento maior que zero.

Encargos de Barramento de Serviço para o número máximo de conexões orientadas simultâneas orientadas que excedem a quantidade incluída (1.000 na camada Standard). Os picos são medidos por hora, rateados dividindo por 744 horas por mês e adicionados ao longo do período de faturamento mensal. A quantidade incluída (1.000 conexões orientadas por mês) é aplicada ao final do período de cobrança em relação à soma dos picos de hora em hora rateados.

Por exemplo:

1. Cada um dos 10.000 dispositivos se conecta por meio de uma única conexão AMQP e recebe comandos de um tópico do Barramento de Serviço. Os dispositivos enviam eventos de telemetria para um Hub de eventos. Se todos os dispositivos ficam conectados 12 horas por dia, os encargos de conexão a seguir se aplicam (além de quaisquer outros encargos de tópico do Barramento de Serviço): 10.000 conexões * 12 horas * 31 dias / 744 = 5.000 conexões orientadas. Após o limite mensal de 1.000 conexões orientadas, você seria cobrado por 4.000 conexões orientadas, a uma taxa de US $0,03 por conexão orientada, para um total de US $120.
2. 10.000 dispositivos recebem mensagens de uma fila do Barramento de Serviço por meio de HTTP, especificando um tempo limite diferente de zero. Se todos os dispositivos ficam conectados 12 horas por dia, os encargos de conexão a seguir se aplicam (além de quaisquer outros encargos de tópico do Barramento de Serviço): 10.000 conexões de recebimento de HTTP * 12 horas * 31 dias / 744 = 5.000 conexões orientadas.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Os encargos de conexão gerenciada são aplicáveis a filas e tópicos/assinaturas?
Sim. Não há cobranças de conexão para enviar eventos usando HTTP, independentemente do número de sistemas ou dispositivos remetentes. O recebimento de eventos com HTTP usando um tempo limite maior que zero, às vezes chamado de "sondagem longa", gera encargos de conexão gerenciada. Conexões do AMQP geram encargos de conexão gerenciada independentemente se as conexões estão sendo usadas para enviar ou receber. As primeiras 1.000 conexões orientadas em todos os namespaces da camada Standard em uma assinatura do Azure são incluídas sem custo adicional (além do custo base). Como essas permissões são suficientes para cobrir muitos cenários de mensagens de serviço para serviço, os encargos de conexão gerenciada normalmente apenas se tornam relevantes se você planeja usar sondagem longa AMQP ou HTTP com um grande número de clientes; por exemplo, para obter um fluxo de eventos mais eficiente ou habilitar a comunicação bidirecional com muitos dispositivos ou instâncias do aplicativo.

## <a name="next-steps"></a>Próximas etapas
* Para obter detalhes completos sobre os preços do Barramento de Serviço, veja a página [Preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).
* Consulte as [Perguntas frequentes sobre Barramento de Serviço](service-bus-faq.md#pricing) para ver algumas perguntas frequentes comuns sobre preços e cobrança do Barramento de Serviço.

[Azure portal]: https://portal.azure.com
