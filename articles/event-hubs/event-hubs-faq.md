---
title: Perguntas frequentes sobre Hubs de Eventos do Azure | Microsoft Docs
description: Perguntas frequentes (FAQs) sobre os Hubs de Eventos do Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: c4faa071c4f2401fe3e852e787e3b7d4da0c7d44
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-frequently-asked-questions"></a>Perguntas frequentes sobre os Hubs de Eventos

## <a name="general"></a>Geral

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual é a diferença entre os níveis Basic e Standard dos Hubs de Eventos?

A camada Standard dos Hubs de Evento do Azure fornece recursos além daqueles disponíveis na camada Básica. Os recursos abaixo estão incluídos na camada Standard:
* Maior retenção de evento
* Conexões agenciadas adicionais, com uma taxa excedente para além do número incluído
* Mais de um único Grupo de consumidores
* [Captura](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

Para obter mais informações sobre tipos de preço, incluindo Hubs de Eventos Dedicados, veja os [Detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>O que são unidades de produtividade dos Hubs de Eventos?

Você seleciona unidades de taxa de transferência de Hubs de Eventos de forma explícita, por meio do Portal do Azure ou de modelos do Resource Manager de Hubs de Eventos. Unidades de produtividade se aplicam a todos os Hubs de Eventos em um namespace de Hubs de Eventos e cada unidade de produtividade proporciona ao namespace os seguintes recursos:

* Até 1 MB por segundo de eventos de entrada (eventos enviados para um Hub de Eventos), mas não mais de 1.000 eventos de entrada, operações de gerenciamento ou chamadas por segundo à API de controle.
* Até 2 MB por segundo de eventos de saída (eventos consumidos de um Hub de Eventos).
* Até 84 GB de armazenamento de eventos (suficiente para o período de retenção padrão de 24 horas).

As unidades de produtividade dos Hubs de Eventos são cobradas por hora, com base no número máximo de unidades selecionadas durante determinada hora. Você pode [aumentar o número de unidades de taxa de transferência](event-hubs-auto-inflate.md) automaticamente à medida que o uso aumentar.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Como os limites de unidades de produtividade dos Hubs de Eventos são aplicados?
Se a produtividade de entrada total ou a taxa de eventos de entrada total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade totais agregados, os remetentes serão limitados e receberão erros indicando que a cota de entrada foi excedida.

Se a produtividade de saída total ou a taxa de eventos de saída total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade agregados, os receptores serão limitados e receberão erros indicando que a cota de saída foi excedida. As cotas de entrada e saída são aplicadas separadamente, para que nenhum remetente possa fazer com que o consumo de eventos se torne lento e para que um receptor não possa evitar que eventos sejam enviados para um Hub de Eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Há um limite para o número de unidades de produtividade que podem ser selecionadas?
Há uma cota padrão de 20 unidades de produtividade por namespace. Você pode solicitar uma cota maior de unidades de produtividade preenchendo um tíquete de suporte. Além do limite de 20 unidades de produtividade, há pacotes disponíveis de 20 e 100 unidades de produtividade. Observe que o uso de mais de 20 unidades de produtividade impossibilita a alteração do número de unidades de produtividade sem o preenchimento de um tíquete de suporte.

Usando o recurso [Inflação automática](event-hubs-auto-inflate.md), você pode aumentar o número de unidades de taxa de transferência automaticamente à medida que o uso aumentar.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Posso usar uma única conexão AMQP para enviar e receber de vários Hubs de Eventos?
Sim, contanto que todos os Hubs de Eventos estejam no mesmo namespace.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual é o período de retenção máximo para eventos?
O nível Standard dos Hubs de Eventos atualmente dão suporte a um período de retenção máximo de 7 dias. Observe que os Hubs de Eventos não são destinados a funcionarem como um armazenamento de dados permanente. Períodos de retenção superiores a 24 horas destinam-se a cenários em que é conveniente repetir um fluxo de eventos nos mesmos sistemas; por exemplo, para treinar ou verificar um novo modelo de aprendizado de máquina nos dados existentes. Se você precisar de retenção de mensagens por mais de 7 dias, a habilitação da [Captura de Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) efetuará pull dos dados do hub de eventos para a conta de armazenamento ou conta de serviço Azure Data Lake de sua escolha. A habilitação da Captura incorrerá em uma cobrança de acordo com a Unidade de Produtividade adquirida.

### <a name="where-is-azure-event-hubs-available"></a>Onde os Hubs de Eventos do Azure estão disponíveis?
Os Hubs de Eventos do Azure está disponível em todas as regiões do Azure com suporte. Para obter uma lista, visite a página [Regiões do Azure](https://azure.microsoft.com/regions/).  

## <a name="best-practices"></a>Práticas recomendadas

### <a name="how-many-partitions-do-i-need"></a>De quantas partições preciso?
Por favor, lembre sempre que a contagem de partições em um Hub de Eventos não pode ser modificada após a instalação. Com isso em mente, é importante pensar sobre quantas partições você precisa antes de começar. 

Hubs de Eventos são projetados para permitir um único leitor de partição por grupo de consumidores. Na maioria dos casos de uso, a configuração padrão de quatro partições é suficiente. Se você estiver buscando dimensionar o processamento de eventos, convém considerar a inclusão de partições adicionais. Não há nenhum limite específico de taxa de transferência em uma partição, no entanto, a taxa de transferência agregada em seu namespace é limitada pelo número de unidades de taxa de transferência. Conforme você aumenta o número de unidades de taxa de transferência no seu namespace, talvez você deseje partições adicionais para permitir que cada um dos leitores simultâneos alcance sua taxa de transferência máxima.

No entanto, se você tiver um modelo no qual seu aplicativo tem uma afinidade com uma determinada partição, aumentar o número de partições pode não ser útil pra você. Para obter mais informações sobre isso, consulte [disponibilidade e consistência](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre preços?
Para obter todas as informações sobre os preços dos Hubs de Eventos, consulte os [detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Há um custo para a retenção de eventos de Hubs de Eventos por mais de 24 horas?
A camada Standard dos Hubs de Eventos permite a retenção de mensagens por períodos superiores a 24 horas, por um máximo de 7 dias. Se o número total de eventos armazenados exceder o limite de armazenamento para o número de unidades de produtividade selecionadas (84 GB por unidade de produtividade), o tamanho que exceder o limite será cobrado com base na taxa de armazenamento de Blobs do Azure publicada. O limite de armazenamento em cada unidade de produtividade cobre todos os custos de armazenamento para períodos de retenção de 24 horas (o padrão) mesmo que a unidade de produtividade seja usada até o limite máximo de entrada.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como o tamanho do armazenamento de Hubs de Eventos é calculado e cobrado?
O tamanho total de todos os eventos armazenados, incluindo eventuais sobrecargas internas para cabeçalhos de eventos ou estruturas de armazenamento em disco em todos os Hubs de Eventos, é medido durante todo o dia. No final do dia, o tamanho do armazenamento de pico é calculado. O limite de armazenamento diário é calculado com base no número mínimo de unidades de produtividade que foram selecionadas durante o dia (cada unidade de produtividade fornece um limite de 84 GB). Se o tamanho total exceder o limite de armazenamento diário calculado, o armazenamento em excesso será cobrado usando as taxas de armazenamento de Blob do Azure (com base na taxa de **armazenamento com redundância local** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como os eventos de entrada de Hubs de Eventos são calculados?
Cada evento enviado a um Hub de Eventos conta como uma mensagem faturável. Um *evento de entrada* é definido como uma unidade de dados menor ou igual a 64 KB. Qualquer evento menor ou igual a 64 KB de tamanho é considerado um evento faturável. Se o evento for maior que 64 KB, o número de eventos passíveis de cobrança será calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o Hub de Eventos é cobrado como um evento, mas uma mensagem de 96 KB enviada para o Hub de Eventos é cobrada como dois eventos.

Eventos consumidos de um Hub de Eventos, bem como operações de gerenciamento e chamadas de controle, como pontos de verificação, não são contadas como eventos de entrada faturáveis, mas se acumulam no limite de unidade de produtividade.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Cobranças por conexões agenciadas são aplicadas aos Hubs de Eventos?
Cobranças de conexão são aplicadas somente quando o protocolo AMQP é usado. Não há cobranças de conexão para enviar eventos usando HTTP, independentemente do número de sistemas ou dispositivos remetentes. Se você pretende usar o AMQP (por exemplo, para obter transmissões de eventos mais eficientes ou para habilitar a comunicação bidirecional em cenários de comando e controle da IoT), consulte a página [Informações sobre preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/) para obter detalhes sobre quantas conexões estão incluídas em cada camada de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como a Captura de Hubs de Eventos é cobrada?
A Captura de Hubs de Eventos será habilitada quando qualquer Hub de Eventos no namespace tiver o recurso Captura habilitado. A Captura de Hubs de Eventos é cobrada por hora de acordo com a unidade de taxa de transferência comprada. À medida que a contagem das unidades de taxa de transferência aumentar ou diminuir, a cobrança da Captura de Hubs de Eventos refletirá essas mudanças em incrementos de hora cheia. Para obter mais informações sobre a cobrança da Captura de Hubs de Eventos, confira [informações de preços de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Serei cobrado pela conta de armazenamento selecionada para a Captura de Hubs de Eventos?
A Captura usa uma conta de armazenamento fornecida quando habilitado em um Hub de Eventos. Como essa é sua conta de armazenamento, todas as alterações nessa configuração serão cobradas em sua assinatura do Azure.

## <a name="quotas"></a>Cotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existe alguma cota associada aos Hubs de Eventos?
Para obter uma lista de todas as cotas de Hubs de Eventos, consulte [cotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelos Hubs de Eventos e suas ações sugeridas?
Para obter uma lista de exceções possíveis dos Hubs de Eventos, confira [Visão geral das exceções](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Logs de diagnóstico
Os Hubs de Eventos dão suporte a dois tipos de [logs de diagnóstico](event-hubs-diagnostic-logs.md) – logs de erro de Captura e logs operacionais – ambos os quais são representados em json e podem ser ativados por meio do Portal do Azure.

### <a name="support-and-sla"></a>Suporte e SLA
Suporte técnico para os Hubs de Eventos está disponível por meio dos [fóruns da comunidade](https://social.msdn.microsoft.com/forums/azure/home). O suporte para gerenciamento de assinaturas e cobranças é fornecido sem custo adicional.

Para saber mais sobre nosso SLA, veja a página [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Inflar automaticamente de Hubs de Eventos](event-hubs-auto-inflate.md)
