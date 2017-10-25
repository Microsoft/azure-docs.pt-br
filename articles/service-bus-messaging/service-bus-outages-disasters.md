---
title: "Isolando aplicativos do Barramento de Serviço do Azure contra interrupções e desastres | Microsoft Docs"
description: "Técnicas para proteger aplicativos contra uma potencial interrupção do Barramento de Serviço."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fd9fa8ab-f4c4-43f7-974f-c876df1614d4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 6dd9045d7aa8d4dc8b3a1acbe6f927e232d9b505
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Práticas recomendadas para isolar aplicativos contra interrupções e desastres do Barramento de Serviço

Os aplicativos de missão crítica devem funcionar continuamente, mesmo na presença de interrupções ou de desastres não planejados. Este tópico descreve técnicas que podem ser usadas para proteger aplicativos do Barramento de Serviço contra uma potencial interrupção de serviço ou um desastre.

Uma interrupção é definida como a indisponibilidade temporária do Barramento de Serviço do Azure. A interrupção pode afetar alguns componentes do Barramento de Serviço, como um repositório de mensagens ou até mesmo o datacenter inteiro. Depois que o problema tiver sido corrigido, o Barramento de Serviço ficará disponível novamente. Normalmente, uma interrupção não causa a perda de mensagens ou de outros dados. Um exemplo de falha de um componente é a indisponibilidade de um repositório de mensagens específico. Um exemplo de uma paralisação de todo o datacenter é uma falha de energia do datacenter ou uma chave de rede do datacenter com defeito. Uma falha pode durar de alguns minutos até alguns dias.

Um desastre é definido como a perda permanente de uma unidade de escala ou de um datacenter do Barramento de Serviço. O datacenter pode ou não ficar disponível novamente. Normalmente, um desastre causa perda de algumas ou de todas as mensagens ou de outros dados. Exemplos de desastres são incêndios, enchentes ou terremoto.

## <a name="current-architecture"></a>Arquitetura atual
O Barramento de Serviço usa vários repositórios de mensagens para armazenar mensagens enviadas para filas ou tópicos. Uma fila ou um tópico não particionado é atribuído a um repositório de mensagens. Se esse repositório de mensagens não estiver disponível, todas as operações na fila ou no tópico falharão.

Todas as entidades de mensagens do Barramento de Serviço (filas, tópicos, retransmissões) residem em um namespace de serviço, que está associado a um datacenter. O Barramento de Serviço não habilita a replicação geográfica automática de dados, nem permite que um namespace se estenda para vários data centers.

## <a name="protecting-against-acs-outages"></a>Proteção contra interrupções do ACS
Se você estiver usando as credenciais do ACS e ele ficar indisponível, os clientes não poderão mais obter tokens. Os clientes com um token no momento em que o ACS ficar inativo poderão continuar a usar o Barramento de Serviço até que os tokens expirem. A vida útil do token padrão é de 3 horas.

Para proteger contra interrupções do ACS, use tokens de Assinatura de Acesso Compartilhado (SAS). Nesse caso, o cliente autenticará diretamente no Barramento de Serviço assinando um token sem uso com uma chave secreta. As chamadas ao ACS não são mais necessárias. Para saber mais sobre tokens SAS, confira [Autenticação do Barramento de Serviço][Service Bus authentication].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Protegendo filas e tópicos contra falhas do repositório de mensagens
Uma fila ou um tópico não particionado é atribuído a um repositório de mensagens. Se esse repositório de mensagens não estiver disponível, todas as operações na fila ou no tópico falharão. Uma fila particionada, por outro lado, consiste em vários fragmentos. Cada fragmento é armazenado em um repositório de mensagens diferente. Quando uma mensagem é enviada a uma fila ou um tópico particionado, o Barramento de Serviço atribui a mensagem a um dos fragmentos. Se o repositório de mensagens correspondente não estiver disponível, o Barramento de Serviço grava a mensagem em um fragmento diferente, se possível. Para saber mais sobre entidades particionadas, confira [Entidades de mensagens particionadas][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Proteção contra interrupções ou desastres de datacenter
Para permitir um failover entre dois datacenters, você poderá criar um namespace de serviço do Barramento de Serviço em cada datacenter. Por exemplo, o namespace de serviço do Barramento de Serviço **contosoPrimary.servicebus.windows.net** pode estar localizado na região Norte/Central dos Estados Unidos, e **contosoSecondary.servicebus.windows.net** pode estar localizado na região Sul/Central dos EUA. Se uma entidade de mensagens do Barramento de Serviço tiver de permanecer acessível em caso de falha do datacenter, você poderá criar essa entidade em ambos os namespaces.

Para saber mais, confira a seção "Falha do Barramento de Serviço em um datacenter do Azure" em [Padrões de mensagens assíncronas e alta disponibilidade][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protegendo pontos de extremidade de retransmissão contra interrupções ou desastres
A replicação geográfica de pontos de extremidade de retransmissão permite que um serviço que exponha um ponto de extremidade de retransmissão esteja acessível quando houver interrupções do Barramento de Serviço. Para obter a replicação geográfica, o serviço deverá criar dois pontos de extremidade de retransmissão em namespaces diferentes. Os namespaces devem residir em datacenters diferentes e os dois pontos de extremidade devem ter nomes diferentes. Por exemplo, um ponto de extremidade primário pode ser acessado em **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto seu equivalente secundário pode ser acessado em **contosoSecondary.servicebus.windows.net/mySecondaryService**.

O serviço escuta em ambos os pontos de extremidade e um cliente pode invocar o serviço por meio de um dos pontos de extremidade. Um aplicativo cliente escolhe aleatoriamente uma das retransmissões como o ponto de extremidade primário e envia a sua solicitação ao ponto de extremidade ativo. Se a operação falhar com um código de erro, essa falha indicará que o ponto de extremidade de retransmissão não está disponível. O aplicativo abre um canal para o ponto de extremidade de backup e emite a solicitação novamente. Nesse ponto, os pontos de extremidade ativo e o de backup alternam as funções: o aplicativo cliente considera o ponto de extremidade ativo antigo como o novo ponto de extremidade de backup e o ponto de extremidade de backup antigo como o novo ponto de extremidade ativo. Se ambas as operações de envio falharem, as funções das duas entidades permanecerão inalteradas e um erro será retornado.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Protegendo filas e tópicos contra interrupções ou desastres do datacenter
Para obter resiliência contra interrupções de datacenter ao usar o sistema de mensagens agenciado, o Barramento de Serviço dá suporte a duas abordagens: replicação *ativa* e *passiva*. Para cada abordagem, se um determinado tópico ou fila deve permanecer acessível em caso de falha do datacenter, você poderá criá-lo em ambos os namespaces. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária pode ser acessada em **contosoPrimary.servicebus.windows.net/myQueue**, enquanto sua equivalente secundária pode ser acessada em **contosoSecondary.servicebus.windows.net/myQueue**.

Se o aplicativo não exigir comunicação permanente do remetente para receptor, o aplicativo poderá implementar uma fila durável do lado do cliente para evitar a perda de mensagens e proteger o remetente de erros transitórios do Barramento de Serviço.

## <a name="active-replication"></a>Replicação ativa
A replicação ativa usa entidades em ambos os namespaces para todas as operações. Qualquer cliente que enviar uma mensagem enviará duas cópias da mesma mensagem. A primeira cópia é enviada para a entidade principal (por exemplo, **contosoPrimary.servicebus.windows.net/sales**) e a segunda cópia da mensagem é enviada para a entidade secundária (por exemplo, **contosoSecondary.servicebus.windows.net/sales**).

Um cliente recebe mensagens de ambas as filas. O receptor processa a primeira cópia de uma mensagem e a segunda cópia é suprimida. Para suprimir mensagens duplicadas, o remetente deverá marcar cada mensagem com um identificador exclusivo. Ambas as cópias da mensagem devem ser marcadas com o mesmo identificador. Você pode usar as propriedades [BrokeredMessage.MessageId][BrokeredMessage.MessageId] ou [BrokeredMessage.Label][BrokeredMessage.Label] ou uma propriedade personalizada para marcar a mensagem. O receptor deve manter uma lista de mensagens que já recebeu.

O exemplo [Replicação geográfica com mensagens agenciadas do Barramento de Serviço][Geo-replication with Service Bus Brokered Messages] demonstra a replicação ativa de entidades de mensagens.

> [!NOTE]
> A abordagem de replicação ativa dobra o número de operações e, portanto, essa abordagem pode gerar custos mais altos.
> 
> 

## <a name="passive-replication"></a>Replicação passiva
No caso sem falhas, a replicação passiva usará apenas uma das duas entidades de mensagens. Um cliente envia a mensagem para a entidade ativa. Se a operação na entidade ativa falhar com um código de erro que indique que o datacenter que hospeda a entidade ativa pode estar indisponível, o cliente enviará uma cópia da mensagem para a entidade de backup. Nesse ponto, as entidades ativa e a de backup alternam as funções: o aplicativo cliente considera a entidade ativa antiga como a nova entidade de backup e entidades de backup antiga como a nova entidade ativa. Se ambas as operações de envio falharem, as funções das duas entidades permanecerão inalteradas e um erro será retornado.

Um cliente recebe mensagens de ambas as filas. Como há uma chance de que o receptor receba duas cópias da mesma mensagem, o receptor deverá suprimir mensagens duplicadas. Você pode suprimir duplicatas da mesma maneira como descrito para a replicação ativa.

Em geral, a replicação passiva é mais econômica do que a replicação ativa porque, na maioria dos casos, somente uma operação é executada. A latência, a taxa de transferência e o custo monetário são idênticos aos do cenário não replicado.

Quando a replicação passiva for usada, as mensagens de cenário a seguir poderão ser perdidas ou recebidas duas vezes:

* **Perda ou atraso de mensagens**: suponha que o remetente tenha enviado com êxito uma mensagem m1 para a fila primária e então a fila ficou indisponível antes do destinatário receber m1. O remetente envia uma mensagem subsequente m2 para a fila secundária. Se a fila primária estiver temporariamente indisponível, o destinatário receberá m1 depois que a fila ficar disponível novamente. Em caso de desastre, talvez o receptor nunca receba m1.
* **Recebimento duplicado**: suponha que o remetente enviar uma mensagem m para a fila primária. O Barramento de Serviço processa m com êxito, mas falha ao enviar uma resposta. Depois que a operação de envio atingir o tempo limite, o remetente enviará uma cópia idêntica de m para a fila secundária. Se o receptor for capaz de receber a primeira cópia de m antes da fila primária ficar indisponível, o receptor receberá ambas as cópias de m aproximadamente ao mesmo tempo. Se o receptor não for capaz de receber a primeira cópia de m antes da fila primária ficar indisponível, inicialmente o destinatário receberá somente a segunda cópia de m, mas depois receberá uma segunda cópia de m quando a fila primária ficar disponível.

O exemplo [Replicação geográfica com mensagens agenciadas do Barramento de Serviço][Geo-replication with Service Bus Brokered Messages] demonstra a replicação passiva de entidades de mensagens.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a recuperação de desastres, confira estes artigos:

* [Continuidade dos negócios no Banco de dados SQL do Azure][Azure SQL Database Business Continuity]
* [Projetando aplicativos resilientes para Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency
