---
author: baanders
description: incluir arquivo para os recursos necessários para criar pontos de extremidade do Azure digital gêmeos
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "99054493"
---
### <a name="prerequisite-create-endpoint-resources"></a>Pré-requisito: criar recursos de ponto de extremidade

Para vincular um ponto de extremidade ao Azure digital gêmeos, o tópico da grade de eventos, o Hub de eventos ou o tópico do barramento de serviço que você está usando para o ponto de extremidade precisa já existir.

Use o gráfico a seguir para ver quais recursos devem ser configurados antes de criar seu ponto de extremidade.

| Tipo de ponto de extremidade | Recursos necessários (vinculados às instruções de criação) |
| --- | --- |
| Ponto de extremidade da grade de eventos | [tópico da grade de eventos](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Ponto de extremidade dos hubs de eventos | [&nbsp;Namespace de hubs de eventos &nbsp;](../articles/event-hubs/event-hubs-create.md)<br/><br/>[Hub de eventos](../articles/event-hubs/event-hubs-create.md)<br/><br/>Adicional [regra de autorização](../articles/event-hubs/authorize-access-shared-access-signature.md) para autenticação baseada em chave | 
| Ponto de extremidade do Barramento de Serviço | [Namespace do Barramento de Serviço](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Tópico do Barramento de Serviço](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> Adicional [regra de autorização](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) para autenticação baseada em chave|
