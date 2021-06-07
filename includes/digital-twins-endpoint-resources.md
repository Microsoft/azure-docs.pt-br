---
author: baanders
description: incluir arquivo para os recursos que são necessários para criar pontos de extremidade dos Gêmeos Digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99054493"
---
### <a name="prerequisite-create-endpoint-resources"></a>Pré-requisito: criar recursos de ponto de extremidade

Para vincular um ponto de extremidade aos Gêmeos Digitais do Azure, o tópico da grade de eventos, o hub de eventos ou o tópico do barramento de serviço que você está usando para o ponto de extremidade precisa já existir.

Use o gráfico a seguir para ver quais recursos devem ser configurados antes de criar seu ponto de extremidade.

| Tipo de ponto de extremidade | Recursos necessários (vinculados às instruções de criação) |
| --- | --- |
| Ponto de extremidade da Grade de Eventos | [tópico da grade de eventos](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Ponto de extremidade do Hubs de Eventos | [Namespace&nbsp;do Hubs&nbsp;de Eventos](../articles/event-hubs/event-hubs-create.md)<br/><br/>[hub de evento](../articles/event-hubs/event-hubs-create.md)<br/><br/>(Adicional) [regra de autorização](../articles/event-hubs/authorize-access-shared-access-signature.md) para autenticação baseada em chave | 
| Ponto de extremidade do Barramento de Serviço | [Namespace do Barramento de Serviço](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Tópico do Barramento de Serviço](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (Adicional) [regra de autorização](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) para autenticação baseada em chave|
