---
title: Grade de eventos do Azure-eventos de parceiro
description: Envie eventos de parceiros de PaaS e SaaS da Grade de Eventos de terceiros diretamente para os serviços do Azure com a Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506134"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Eventos de parceiro na grade de eventos do Azure (visualização)
O recurso de **eventos de parceiro** permite que um provedor de SaaS de terceiros publique eventos de seus serviços para que os consumidores possam assinar esses eventos. Esse recurso oferece uma experiência de terceiros a fontes de eventos de terceiros, expondo um tipo de [tópico](concepts.md#topics) , um **tópico de parceiro**. Os assinantes criam assinaturas para este tópico para consumir eventos. Ele também fornece um modelo de pub-sub limpo, separando as preocupações e a propriedade dos recursos que são usados por editores de eventos e assinantes.

> [!NOTE]
> Se você for novo no usando a grade de eventos, consulte [visão geral](overview.md), [conceitos](concepts.md)e [manipuladores de eventos](event-handlers.md).

## <a name="what-is-partner-events-to-a-publisher"></a>O que são os eventos de parceiro para um Publicador?
Para um editor de eventos, o recurso de eventos de parceiro permite que os editores realizem as seguintes tarefas:

- Integrar suas origens de eventos à grade de eventos
- Criar um namespace (ponto de extremidade) no qual eles podem publicar eventos
- Criar tópicos de parceiro no Azure que os assinantes possuem e usam para consumir eventos

## <a name="what-is-partner-events-to-a-subscriber"></a>O que são os eventos de parceiro para um assinante?
Para um assinante, o recurso de eventos de parceiro permite que eles criem tópicos de parceiros no Azure para consumir eventos de fontes de eventos de terceiros. O consumo de eventos é realizado pela criação de assinaturas de evento que enviam eventos (push) para o manipulador de eventos de um assinante.

## <a name="why-should-i-use-partner-events"></a>Por que devo usar eventos de parceiro?
Talvez você queira usar os eventos de parceiro se tiver um ou mais dos requisitos a seguir.

### <a name="for-publishers"></a>Para editores

- Você deseja que um mecanismo torne seus eventos disponíveis no Azure. Os usuários podem filtrar e rotear esses eventos usando tópicos de parceiros e assinaturas de eventos que eles possuem e gerenciam. Você pode usar outras abordagens de integração, como [Tópicos](custom-topics.md) e [domínios](event-domains.md). Mas eles não permitiriam uma separação clara de propriedade, gerenciamento e cobrança de recursos (tópicos de parceiros) entre Publicadores e assinantes. Além disso, essa abordagem fornece uma experiência de usuário mais intuitiva que torna mais fácil descobrir e usar tópicos de parceiros.
- Você deseja publicar eventos em um único ponto de extremidade, o ponto de extremidade do namespace. E você deseja a capacidade de filtrar esses eventos para que apenas um subconjunto deles esteja disponível. 
- Você deseja ter visibilidade das métricas relacionadas aos eventos publicados.
- Você deseja usar o esquema de [eventos de nuvem 1,0](https://cloudevents.io/) para seus eventos.

### <a name="for-subscribers"></a>Para assinantes

- Você deseja assinar eventos de [Publicadores terceirizados](#available-third-party-event-publishers) e manipular os eventos usando manipuladores de eventos que estão no Azure ou em outro lugar.
- Você deseja aproveitar o rico conjunto de recursos de roteamento e de [destinos/manipuladores de eventos](overview.md#event-handlers) para processar eventos de fontes de terceiros. 
- Você deseja implementar arquiteturas menos rígidas em que o seu manipulador de assinante/evento não está ciente da existência do agente de mensagem usado. 
- Você precisa de um mecanismo de entrega por push resiliente com suporte de repetição de envio e semântica pelo menos uma vez.
- Você deseja usar o esquema de [eventos de nuvem 1,0](https://cloudevents.io/) para seus eventos. 


## <a name="available-third-party-event-publishers"></a>Publicadores de eventos de terceiros disponíveis
Um Publicador de eventos de terceiros deve passar por um [processo de integração](partner-onboarding-overview.md) antes que um assinante possa começar a consumir seus eventos. 

Se você for um assinante e desejar que um serviço de terceiros exponha seus eventos por meio da grade de eventos, 

### <a name="auth0"></a>Auth0
**Auth0** é o primeiro Publicador de parceiros disponível. Você pode criar um [tópico de parceiro Auth0](auth0-overview.md) para conectar suas contas do Auth0 e do Azure. Essa integração permite reagir, registrar e monitorar eventos Auth0 em tempo real. Para experimentar, consulte [integrar a grade de eventos do Azure com Auto0](auth0-how-to.md)

Se você quiser que um serviço de terceiros exponha seus eventos por meio da grade de eventos, envie a ideia no [portal de voz do usuário](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Recursos gerenciados por editores de eventos
Os editores de eventos criam e gerenciam os seguintes recursos:

### <a name="partner-registration"></a>Registro de parceiro
Um registro contém informações gerais relacionadas a um Publicador. Ele define um tipo de tópico de parceiro que mostra no portal do Azure como uma opção quando os usuários tentam criar um tópico de parceiro. Um Publicador pode expor mais de um ou mais tipos de tópico de parceiros para atender às necessidades de seus assinantes. Ou seja, um Publicador pode criar registros separados (tipos de tópico de parceiros) para eventos de diferentes serviços. Por exemplo, para o serviço de RH (recursos humanos), o Publicador pode definir um tópico de parceiro para eventos como um funcionário ingressado, funcionário promovido e funcionário saiu da empresa. 

Tenha em mente os seguintes pontos:

- Somente os registros de parceiros aprovados pelo Azure são visíveis. 
- Os registros são globais. Ou seja, eles não estão associados a uma região específica do Azure.
- Um registro é um recurso opcional. Mas é recomendável que você (como um Publicador) crie um registro. Ele permite que os usuários descubram seus tópicos na página **Criar tópico do parceiro** na [portal do Azure](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic). Em seguida, o usuário pode selecionar os tipos de evento (por exemplo, ingressado no funcionário, o funcionário à esquerda e assim por diante) durante a criação de assinaturas de evento.

### <a name="namespace"></a>Namespace
Como os tópicos e os [domínios](event-domains.md) [personalizados](custom-topics.md) , um namespace de parceiro é um ponto de extremidade regional para publicar eventos. É por meio de namespaces que os editores criam e gerenciam canais de eventos. Um namespace também funciona como o recurso de contêiner para canais de eventos.

### <a name="event-channels"></a>Canais de eventos
Um canal de eventos é um recurso espelhado em um tópico de parceiro. Quando um Publicador cria um canal de evento na assinatura do Azure do Publicador, ele também cria um tópico de parceiro sob a assinatura do Azure de um assinante. As operações feitas em relação a um canal de evento (exceto GET) serão aplicadas ao tópico de parceiro do assinante correspondente, até mesmo exclusão. No entanto, somente os tópicos de parceiros são os tipos de recursos nos quais as assinaturas e a entrega de eventos podem ser configuradas.

## <a name="resources-managed-by-subscribers"></a>Recursos gerenciados por assinantes 
Os assinantes podem usar tópicos de parceiros definidos por um Publicador e é o único tipo de recurso que eles veem e gerenciam. Depois que um tópico de parceiro é criado, um usuário do assinante pode criar assinaturas de evento definindo regras de filtro para [destinos/manipuladores de eventos](overview.md#event-handlers). Para assinantes, um tópico de parceiro e suas assinaturas de evento associadas fornecem os mesmos recursos avançados que os [Tópicos personalizados](custom-topics.md) e suas assinaturas relacionadas fazem com uma diferença notável: os tópicos de parceiros dão suporte apenas ao [esquema de eventos de nuvem 1,0](cloudevents-schema.md), que fornece um conjunto mais rico de recursos do que outros esquemas com suporte.

A imagem a seguir mostra o fluxo de operações do plano de controle.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="Eventos de parceiro – fluxo do plano de controle":::

1. O Publicador cria um **registro de parceiro**. Os registros de parceiros são globais. Ou seja, eles não estão associados a uma região específica do Azure. Esta etapa é opcional.
1. O editor cria um **namespace de parceiro** em uma região específica.
1. Quando o assinante 1 tenta criar um tópico de parceiro, um **canal de evento** , canal de evento 1, é criado na assinatura do Azure do Publicador primeiro.
1. Em seguida, um **tópico de parceiro** , tópico de parceiro 1, é criado na assinatura do Azure do Assinante. O assinante precisa ativar o tópico parceiro. 
1. O assinante 1 cria uma **assinatura dos aplicativos lógicos do Azure** para o tópico 1 do parceiro.
1. O assinante 1 cria uma **assinatura de armazenamento de BLOBs do Azure** para o tópico de parceiro 1. 
1. Quando o assinante 2 tenta criar um tópico de parceiro, outro **canal de evento** , canal de evento 2, é criado na assinatura do Azure do Publicador primeiro. 
1. Em seguida, o **tópico de parceiro** , o tópico de parceiro 2, é criado na assinatura do Azure do segundo assinante. O assinante precisa ativar o tópico parceiro. 
1. O assinante 2 cria uma **assinatura Azure Functions** para o tópico do parceiro 2. 

## <a name="pricing"></a>Preços
Os tópicos de parceiros são cobrados pelo número de operações realizadas ao usar a grade de eventos. Para obter mais informações sobre todos os tipos de operações que são usadas como base para cobrança e informações detalhadas de preço, consulte [preços da grade de eventos](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="limits"></a>Limites
Consulte [limites do serviço de grade de eventos](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) para obter informações detalhadas sobre os limites em vigor para tópicos de parceiros.


## <a name="next-steps"></a>Próximas etapas

- [Tópico de parceiro do Auth0](auth0-overview.md)
- [Como usar o tópico de parceiro do Auth0](auth0-how-to.md)
- [Torne-se um Parceiro da Grade de Eventos](partner-onboarding-overview.md)