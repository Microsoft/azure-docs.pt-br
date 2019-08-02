---
title: Isolando aplicativos do Barramento de Serviço do Azure contra interrupções e desastres | Microsoft Docs
description: Técnicas para proteger aplicativos contra uma potencial interrupção do Barramento de Serviço.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24fba1961c8fd95f1b9489716d690dd6eaa97b62
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274837"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Práticas recomendadas para isolar aplicativos contra interrupções e desastres do Barramento de Serviço

Os aplicativos de missão crítica devem funcionar continuamente, mesmo na presença de interrupções ou de desastres não planejados. Este artigo descreve técnicas que podem ser usadas para proteger aplicativos do Barramento de Serviço contra uma potencial interrupção de serviço ou um desastre.

Uma interrupção é definida como a indisponibilidade temporária do Barramento de Serviço do Azure. A interrupção pode afetar alguns componentes do Barramento de Serviço, como um repositório de mensagens ou até mesmo o datacenter inteiro. Depois que o problema tiver sido corrigido, o Barramento de Serviço ficará disponível novamente. Normalmente, uma interrupção não causa a perda de mensagens ou de outros dados. Um exemplo de falha de um componente é a indisponibilidade de um repositório de mensagens específico. Um exemplo de uma paralisação de todo o datacenter é uma falha de energia do datacenter ou uma chave de rede do datacenter com defeito. Uma falha pode durar de alguns minutos até alguns dias.

Um desastre é definido como a perda permanente de uma unidade de escala ou de um datacenter do Barramento de Serviço. O datacenter pode ou não ficar disponível novamente. Normalmente, um desastre causa perda de algumas ou de todas as mensagens ou de outros dados. Exemplos de desastres são incêndios, enchentes ou terremoto.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Proteção contra interrupções e desastres: Barramento de Serviço Premium
Os conceitos de Alta disponibilidade e Recuperação de desastres são integrados diretamente à camada do Barramento de Serviço Premium do Azure, ambos na mesma região (por meio de Zonas de disponibilidade) e em diferentes regiões (via Recuperação de desastre geográfico).

### <a name="geo-disaster-recovery"></a>Recuperação de desastre geográfico

O Barramento de Serviço Premium oferece suporte à recuperação de desastre geográfico no nível do namespace. Para mais informações consulte [Recuperação de desastre em área geográfica do Barramento de Serviço do Azure](service-bus-geo-dr.md). O recurso de recuperação de desastre, disponível apenas para [SKU Premium](service-bus-premium-messaging.md), implementa a recuperação de desastre dos metadados e se baseia em namespaces de recuperação de desastre primário e secundário.

### <a name="availability-zones"></a>Zonas de Disponibilidades

O SKU do Barramento de Serviço Premium oferece suporte às [Zonas de Disponibilidade](../availability-zones/az-overview.md), fornecendo locais isolados de falhas dentro da mesma região do Azure.

> [!NOTE]
> O suporte a Zonas de Disponibilidade para o Barramento de Serviço Premium do Azure só é oferecido nas [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) em que existem zonas de disponibilidade.

Você pode habilitar as Zonas de Disponibilidade apenas em novos namespaces usando o portal do Azure. O Barramento de Serviço não dá suporte à migração dos namespaces existentes. Você não pode desabilitar a redundância de zona depois de habilitá-la em seu namespace.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Proteção contra interrupções e desastres: Barramento de Serviço Standard
Para obter resiliência contra interrupções de datacenter ao usar a camada de preços do sistema de mensagens Standard, o Barramento de Serviço oferece suporte a duas abordagens: replicação *ativa* e *passiva*. Para cada abordagem, se um determinado tópico ou fila deve permanecer acessível em caso de falha do datacenter, você poderá criá-lo em ambos os namespaces. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária pode ser acessada em **contosoPrimary.servicebus.windows.net/myQueue**, enquanto sua equivalente secundária pode ser acessada em **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> As configurações da **Replicação ativa** e da **Replicação passiva** são soluções de uso geral e recursos não específicos do Barramento de Serviço. A lógica de replicação (enviando para dois namespaces diferentes) reside nos aplicativos remetentes e o receptor deve ter a lógica personalizada para detecção de duplicidades.

Se o aplicativo não exigir comunicação permanente do remetente para receptor, o aplicativo poderá implementar uma fila durável do lado do cliente para evitar a perda de mensagens e proteger o remetente de erros transitórios do Barramento de Serviço.

### <a name="active-replication"></a>Replicação ativa
A replicação ativa usa entidades em ambos os namespaces para todas as operações. Qualquer cliente que enviar uma mensagem enviará duas cópias da mesma mensagem. A primeira cópia é enviada para a entidade principal (por exemplo, **contosoPrimary.servicebus.windows.net/sales**) e a segunda cópia da mensagem é enviada para a entidade secundária (por exemplo, **contosoSecondary.servicebus.windows.net/sales**).

Um cliente recebe mensagens de ambas as filas. O receptor processa a primeira cópia de uma mensagem e a segunda cópia é suprimida. Para suprimir mensagens duplicadas, o remetente deverá marcar cada mensagem com um identificador exclusivo. Ambas as cópias da mensagem devem ser marcadas com o mesmo identificador. Você pode usar o [brokeredmessage. MessageID][BrokeredMessage.MessageId] ou [brokeredmessage. Label][BrokeredMessage.Label] propriedades ou uma propriedade personalizada para marcar a mensagem. O receptor deve manter uma lista de mensagens que já recebeu.

O [replicação geográfica com a camada Standard do barramento de serviço][Geo-replication with Service Bus Standard Tier] exemplo demonstra a replicação ativa de entidades de mensagens.

> [!NOTE]
> A abordagem de replicação ativa dobra o número de operações e, portanto, essa abordagem pode gerar custos mais altos.
> 
> 

### <a name="passive-replication"></a>Replicação passiva
No caso sem falhas, a replicação passiva usará apenas uma das duas entidades de mensagens. Um cliente envia a mensagem para a entidade ativa. Se a operação na entidade ativa falhar com um código de erro que indique que o datacenter que hospeda a entidade ativa pode estar indisponível, o cliente enviará uma cópia da mensagem para a entidade de backup. Nesse ponto, as entidades ativa e a de backup alternam as funções: o aplicativo cliente considera a entidade ativa antiga como a nova entidade de backup e entidades de backup antiga como a nova entidade ativa. Se ambas as operações de envio falharem, as funções das duas entidades permanecerão inalteradas e um erro será retornado.

Um cliente recebe mensagens de ambas as filas. Como há uma chance de que o receptor receba duas cópias da mesma mensagem, o receptor deverá suprimir mensagens duplicadas. Você pode suprimir duplicatas da mesma maneira como descrito para a replicação ativa.

Em geral, a replicação passiva é mais econômica do que a replicação ativa porque, na maioria dos casos, somente uma operação é executada. A latência, a taxa de transferência e o custo monetário são idênticos aos do cenário não replicado.

Quando a replicação passiva for usada, as mensagens de cenário a seguir poderão ser perdidas ou recebidas duas vezes:

* **Perda ou atraso de mensagens**: Suponha que o remetente tenha enviado com êxito uma mensagem m1 para a fila primária e então a fila ficou indisponível antes do destinatário receber m1. O remetente envia uma mensagem subsequente m2 para a fila secundária. Se a fila primária estiver temporariamente indisponível, o destinatário receberá m1 depois que a fila ficar disponível novamente. Em caso de desastre, talvez o receptor nunca receba m1.
* **Recebimento duplicado**: Suponha que o remetente envie uma mensagem m para a fila primária. O Barramento de Serviço processa m com êxito, mas falha ao enviar uma resposta. Depois que a operação de envio atingir o tempo limite, o remetente enviará uma cópia idêntica de m para a fila secundária. Se o receptor for capaz de receber a primeira cópia de m antes da fila primária ficar indisponível, o receptor receberá ambas as cópias de m aproximadamente ao mesmo tempo. Se o receptor não for capaz de receber a primeira cópia de m antes da fila primária ficar indisponível, inicialmente o destinatário receberá somente a segunda cópia de m, mas depois receberá uma segunda cópia de m quando a fila primária ficar disponível.

O [replicação geográfica com a camada Standard do barramento de serviço][Geo-replication with Service Bus Standard Tier] exemplo demonstra a replicação passiva de entidades de mensagens.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protegendo pontos de extremidade de retransmissão contra interrupções ou desastres
A replicação geográfica de [retransmissão do Azure](../service-bus-relay/relay-what-is-it.md) pontos de extremidade permite que um serviço que expõe um ponto de extremidade de retransmissão esteja acessível quando houver interrupções do barramento de serviço. Para obter a replicação geográfica, o serviço deverá criar dois pontos de extremidade de retransmissão em namespaces diferentes. Os namespaces devem residir em datacenters diferentes e os dois pontos de extremidade devem ter nomes diferentes. Por exemplo, um ponto de extremidade primário pode ser acessado em **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto seu equivalente secundário pode ser acessado em **contosoSecondary.servicebus.windows.net/mySecondaryService**.

O serviço escuta em ambos os pontos de extremidade e um cliente pode invocar o serviço por meio de um dos pontos de extremidade. Um aplicativo cliente escolhe aleatoriamente uma das retransmissões como o ponto de extremidade primário e envia a sua solicitação ao ponto de extremidade ativo. Se a operação falhar com um código de erro, essa falha indicará que o ponto de extremidade de retransmissão não está disponível. O aplicativo abre um canal para o ponto de extremidade de backup e emite a solicitação novamente. Nesse ponto, os pontos de extremidade ativo e o de backup alternam as funções: o aplicativo cliente considera o ponto de extremidade ativo antigo como o novo ponto de extremidade de backup e o ponto de extremidade de backup antigo como o novo ponto de extremidade ativo. Se ambas as operações de envio falharem, as funções das duas entidades permanecerão inalteradas e um erro será retornado.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a recuperação de desastres, confira estes artigos:

* [Recuperação de desastre em área geográfica do Barramento de Serviço do Azure](service-bus-geo-dr.md)
* [Continuidade dos negócios no Banco de dados SQL do Azure][Azure SQL Database Business Continuity]
* [Projeto de aplicativos resilientes do Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
