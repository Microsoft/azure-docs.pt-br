---
title: Visão geral da grade de eventos do cache do Azure para Redis
description: Use a grade de eventos do Azure para publicar o cache do Azure para eventos Redis.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055658"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Visão geral da grade de eventos do cache do Azure para Redis 

O cache do Azure para eventos Redis, como aplicação de patch, dimensionamento, importação/exportação (RDB) eventos são enviados por push usando a [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como Azure functions, aplicativos lógicos do Azure ou até mesmo para seu próprio ouvinte de http. A grade de eventos fornece entrega de eventos confiável para seus aplicativos por meio de políticas de repetição avançadas e mensagens mortas.

Consulte o artigo [sobre o esquema do cache do Azure para eventos Redis](../event-grid/event-schema-azure-cache.md) para exibir a lista completa dos eventos aos quais o cache do Azure para Redis dá suporte.

Se você quiser experimentar o cache do Azure para eventos Redis, consulte qualquer um destes inícios rápidos:

|Se você quiser usar essa ferramenta:    |Consulte este guia de início rápido: |
|--|-|
|Portal do Azure    |[Início rápido: encaminhar o cache do Azure para eventos Redis para o ponto de extremidade da Web com o portal do Azure](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Início rápido: encaminhe o cache do Azure para eventos Redis para o ponto de extremidade da Web com o PowerShell](cache-event-grid-quickstart-powershell.md)|
|CLI do Azure    |[Início rápido: encaminhar o cache do Azure para eventos Redis para o ponto de extremidade da Web com CLI do Azure](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>O modelo de evento

A grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Essa imagem ilustra a relação entre os editores de eventos, as assinaturas de evento e os manipuladores de eventos.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Modelo de grade de eventos.":::

Primeiro, assine um ponto de extremidade para um evento. Em seguida, quando um evento for disparado, o serviço de grade de eventos enviará dados sobre esse evento para o ponto de extremidade.

Consulte o artigo [esquema do cache do Azure para eventos Redis](../event-grid/event-schema-azure-cache.md) para exibir:

> [!div class="checklist"]
> * Uma lista completa do cache do Azure para eventos Redis e como cada evento é disparado.
> * Um exemplo dos dados que a grade de eventos enviaria para cada um desses eventos.
> * A finalidade de cada par chave-valor que aparece nos dados.


## <a name="best-practices-for-consuming-events"></a>Práticas recomendadas para o consumo de eventos

Os aplicativos que lidam com o cache do Azure para eventos Redis devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, é importante não pressupor que os eventos sejam de uma fonte específica, mas para verificar o tópico da mensagem a fim de garantir que ele venha do cache do Azure para a instância Redis que você espera.
> * Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
> * O cache do Azure para eventos Redis garante a entrega pelo menos uma vez aos assinantes, o que garante que todas as mensagens sejam emitidas. No entanto, devido a novas tentativas ou à disponibilidade de assinaturas, ocasionalmente podem ocorrer mensagens duplicadas. Para saber mais sobre a entrega e a repetição de mensagens, confira [entrega e repetição de mensagem da grade de eventos](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a grade de eventos e dê uma tentativa ao cache do Azure para eventos Redis:

- [Sobre a Grade de Eventos](../event-grid/overview.md)
- [Cache do Azure para esquema de eventos Redis](../event-grid/event-schema-azure-cache.md)
- [Encaminhar o cache do Azure para eventos Redis para o ponto de extremidade da Web com CLI do Azure](cache-event-grid-quickstart-cli.md)
- [Encaminhar o cache do Azure para eventos Redis para o ponto de extremidade da Web com o portal do Azure](cache-event-grid-quickstart-portal.md)
- [Encaminhar o cache do Azure para eventos Redis para o ponto de extremidade da Web com o PowerShell](cache-event-grid-quickstart-powershell.md)
