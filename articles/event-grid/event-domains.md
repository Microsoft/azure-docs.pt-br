---
title: Domínios de Eventos na Grade de Eventos do Azure
description: Descreve como os Domínios de Eventos são usados para gerenciar tópicos na Grade de Eventos do Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: ef0a9213d095d0b7ae4343e2af145236a7e005a1
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305416"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Entenda os domínios de eventos para gerenciar tópicos do Event Grid

Este artigo descreve como usar domínios de eventos para gerenciar o fluxo de eventos personalizados para várias organizações comerciais, clientes ou aplicativos. Use domínios de eventos para:

* Gerenciar arquiteturas de eventos multilocatário em escala.
* Gerenciar a autorização e a autenticação.
* Particionar tópicos sem gerenciar cada um individualmente.
* Evitar publicar individualmente em cada um dos pontos de extremidade do tópico.

## <a name="event-domain-overview"></a>Visão geral do domínio de eventos

Um domínio de evento é uma ferramenta de gerenciamento para um grande número de tópicos do Event Grid relacionados ao mesmo aplicativo. Você pode pensar nisso como um meta-tópico que pode ter milhares de tópicos individuais.

Os domínios de eventos disponibilizam a mesma arquitetura usada pelos serviços do Azure (como Armazenamento e Hub IoT) para publicar seus eventos. Com eles, é possível publicar eventos em milhares de tópicos. Os Domínios também permitem que você controle a autorização e a autenticação de cada tópico, para que seja possível particionar seus locatários.

### <a name="example-use-case"></a>Caso de uso de exemplo

Os domínios de eventos são mais facilmente explicados usando um exemplo. Digamos que você execute o Maquinário de Construção Contoso na fabricação de tratores, equipamentos de escavação e outras máquinas pesadas. Como parte da execução dos negócios, você envia informações em tempo real aos clientes sobre manutenção de equipamentos, integridade de sistemas e atualizações de contrato. Todas essas informações vão para vários pontos de extremidade, incluindo seu aplicativo, endpoints de clientes e outras infraestruturas que os clientes configuraram.

Todas essas informações vão para vários pontos de extremidade, incluindo seu aplicativo, endpoints de clientes e outras infraestruturas que os clientes configuraram. Cada um dos seus clientes é representado como um tópico dentro do domínio. Autenticação e autorização são tratadas usando o Active Directory do Azure. Cada um de seus clientes pode se inscrever no tópico deles e receber os eventos deles. O acesso gerenciado por meio do domínio do evento garante que eles possam acessar apenas o tópico deles.

Além disso, também fornece um ponto de extremidade exclusivo, em que todos os eventos de cliente podem ser publicados. A Event Grid cuidará de garantir que cada tópico esteja ciente dos eventos definidos para o locatário.

![Exemplo de construção Contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>gerenciamento de acesso

Com um domínio, você obtém autorização de granulação fina e controle de autenticação sobre cada tópico através do controle de acesso baseado em função (RBAC) do Azure. É possível usar essas funções para restringir cada locatário no aplicativo somente aos tópicos que você deseja permitir acesso.

O RBAC em domínios de eventos funciona da mesma maneira que o [controle de acesso gerenciado](security-authentication.md#management-access-control) funciona no restante da Grade de Eventos e do Azure. Use o RBAC para criar e impor definições de função personalizadas em domínios de eventos.

### <a name="built-in-roles"></a>Funções internas

O Event Grid tem duas definições de função integradas para tornar o RBAC mais fácil para trabalhar com domínios de eventos. Essas funções são **EventGrid EventSubscription Contributor (Visualizar)** e **EventGrid EventSubscription Reader (Visualizar)** . Você atribui essas funções a usuários que precisam se inscrever em tópicos em seu domínio de evento. Você faz o escopo da atribuição de função apenas para o tópico que os usuários precisam assinar.

Para obter informações sobre essas funções, consulte [Funções internas da grade de eventos](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Assinar tópicos

Assinar eventos em um tópico em um domínio de evento é o mesmo que [Criar uma Assinatura de evento em um tópico personalizado](./custom-event-quickstart.md) ou inscrever-se em um evento de um serviço do Azure.

### <a name="domain-scope-subscriptions"></a>Assinaturas de escopo de domínio

Os domínios de eventos também permitem assinaturas de escopo de domínio. Uma inscrição de evento em um domínio de evento receberá todos os eventos enviados ao domínio, independentemente do tópico para o qual os eventos são enviados. As assinaturas de escopo de domínio podem ser úteis para fins de gerenciamento e auditoria.

## <a name="publishing-to-an-event-domain"></a>Publicação em um domínio de evento

Quando você cria um domínio de evento, recebe um ponto de extremidade de publicação semelhante a se você criou um tópico na Grade de Eventos. 

Para publicar eventos em qualquer tópico em um Domínio de Eventos, envie os eventos para o endpoint do domínio [da mesma forma que você faria para um tópico personalizado](./post-to-custom-topic.md). A única diferença é que você deve especificar o tópico para o qual você gostaria que o evento seja entregue.

Por exemplo, publicar a matriz de eventos a seguir enviaria o evento com `"id": "1111"` para o tópico `foo`, enquanto o evento com `"id": "2222"` seria enviado para o tópico `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Os domínios de eventos tratam da publicação de tópicos para você. Em vez de publicar eventos para cada tópico gerenciado individualmente, você pode publicar todos os seus eventos no endpoint do domínio. A Grade de Eventos garante que cada evento seja enviado para o tópico correto.

## <a name="limits-and-quotas"></a>Limites e cotas
Aqui estão os limites e as cotas relacionados aos domínios de evento:

- 100.000 tópicos por domínio de evento 
- 100 domínios de evento por assinatura do Azure 
- 500 assinaturas de evento por tópico em um domínio de eventos
- 50 assinaturas de escopo de domínio 
- taxa de ingestão de eventos por segundo do 5.000 (em um domínio)

Se esses limites não forem adequados a você, acesse a equipe do produto abrindo um tíquete de suporte ou enviando um [askgrid@microsoft.com](mailto:askgrid@microsoft.com)email para. 

## <a name="pricing"></a>Preços
Os domínios de evento usam o mesmo [preço de operações](https://azure.microsoft.com/pricing/details/event-grid/) que todos os outros recursos na grade de eventos usam.

As operações funcionam da mesma forma em domínios de eventos, como em tópicos personalizados. Cada entrada de um evento para um domínio de evento é uma operação e cada tentativa de entrega de um evento é uma operação.

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar domínios de eventos, criar tópicos, criar inscrições de eventos e publicar eventos, consulte [Gerenciar domínios de eventos](./how-to-event-domains.md).
