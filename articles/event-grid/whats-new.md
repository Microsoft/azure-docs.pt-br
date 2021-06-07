---
title: Quais são as novidades? Notas sobre a versão – Grade de Eventos do Azure
description: Conheça as novidades da Grade de Eventos do Azure, como as últimas notas sobre a versão, problemas conhecidos, correções de bugs, funcionalidades preteridas e alterações futuras.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: da0b26e4f163f428e6955a37636ceb19bb34abc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107526"
---
# <a name="whats-new-in-azure-event-grid"></a>O que há de novo na Grade de Eventos do Azure?

>Seja notificado sobre quando revisitar esta página para ver atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` no seu leitor de feed ![ícone do leitor de RSS feed](./media/whats-new/feed-icon-16x16.png).

A Grade de Eventos do Azure é aprimorada continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Adicionar suporte à nova versão 2020-06-01 da API de serviço em GA (disponibilidade geral).
- Os novos recursos que estão em GA:
    - [Mapeamentos de entrada](input-mappings.md)
    - [Esquema de entrada personalizado](input-mappings.md)
    - [Esquema de evento de nuvem V10](cloud-event-schema.md)
    - [Tópico do Barramento de Serviço como destino](handler-service-bus.md)
    - [Função do Azure como destino](handler-functions.md)
    - [Envio em lote de webhooks](./edge/delivery-output-batching.md)
    - [Webhook seguro (suporte ao Azure Active Directory)](secure-webhook-delivery.md)
    - [Filtragem de IP](configure-firewall.md)
    - [Suporte ao Serviço de Link Privado](configure-private-endpoints.md)
    - [Esquema de entrega de eventos](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- Esta versão inclui correções de bugs adicionais para aprimorar a qualidade.
- Como a 5.3.1-preview, esta versão corresponde à versão da API 2020-04-01-Preview, que inclui as seguintes novas funcionalidades: 
    - [Suporte para filtragem de IP ao publicar eventos em domínios e tópicos](configure-firewall.md)
    - [Tópicos de parceiros](./partner-events-overview.md)
    - [Tópicos do sistema como recursos controlados no portal do Azure](system-topics.md)
    - [Entrega de eventos com identidade do serviço gerenciado](managed-service-identity.md) 
    - [Suporte ao Serviço de Link Privado](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- Esta versão inclui diversas correções de bugs para aprimorar a qualidade.
- Como a 5.3.0-preview, esta versão corresponde à versão da API 2020-04-01-Preview, que inclui as seguintes novas funcionalidades: 
    - [Suporte para filtragem de IP ao publicar eventos em domínios e tópicos](configure-firewall.md)
    - [Tópicos de parceiros](./partner-events-overview.md)
    - [Tópicos do sistema como recursos controlados no portal do Azure](system-topics.md)
    - [Entrega de eventos com identidade do serviço gerenciado](managed-service-identity.md) 
    - [Suporte ao Serviço de Link Privado](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- Apresentamos novos recursos além daqueles adicionados na versão 5.2.0-preview. 
- Como versão 5.2.0-preview, esta versão corresponde à versão da API 2020-04-01-Preview.
- Ela adiciona suporte às seguintes novas funcionalidades: 
    - [Suporte para filtragem de IP ao publicar eventos em domínios e tópicos](configure-firewall.md)
    - [Tópicos de parceiros](./partner-events-overview.md)
    - [Tópicos do sistema como recursos controlados no portal do Azure](system-topics.md)
    - [Entrega de eventos com identidade do serviço gerenciado](managed-service-identity.md) 
    - [Suporte ao Serviço de Link Privado](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- Esta versão corresponde à versão da API 2020-04-01-Preview.
- Ela adiciona suporte às seguintes novas funcionalidades:
    - [Suporte para filtragem de IP ao publicar eventos em domínios e tópicos](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Esta versão corresponde à versão da API `2019-06-01`.
- Ela adiciona suporte às seguintes novas funcionalidades:
    * [Domínios](event-domains.md)
    * Paginação e filtro de pesquisa para operações de lista de recursos. Para obter um exemplo, confira [Tópicos – Listar por assinatura](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription).
    * [Fila do Barramento de Serviço como destino](handler-service-bus.md)
    * [Filtragem avançada](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- Esta versão corresponde à versão da API 2019-02-01-preview.
- Ela adiciona suporte às seguintes novas funcionalidades:
    * Paginação e filtro de pesquisa para operações de lista de recursos. Para obter um exemplo, confira [Tópicos – Listar por assinatura](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription).
    * [Criação/exclusão manual de tópicos do domínio](how-to-event-domains.md)
    * [Fila do Barramento de Serviço como destino](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Esta versão corresponde à versão da API estável `2019-01-01`.
- Ela dá suporte à GA (disponibilidade geral) das seguintes funcionalidades relacionadas às assinaturas de evento:
    * [Destino de mensagens mortas](manage-event-delivery.md)
    * [Fila do Armazenamento do Azure como destino](handler-storage-queues.md)
    * [Retransmissão do Azure – Conexão Híbrida como destino](handler-relay-hybrid-connections.md)
    * [Validação de handshake manual](webhook-event-delivery.md)
    * [Suporte para políticas de repetição](delivery-and-retry.md)
- Recursos que ainda estão na versão prévia (como os [Domínios da Grade de Eventos](event-domains.md) ou o [suporte para filtros avançados](event-filtering.md#advanced-filtering) podem ser acessados usando a versão 3.0.1-preview do SDK ".

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- Usando a dependência a [versão 10.0.3 do pacote NuGet Newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- Esta versão é um SDK em versão prévia para os novos recursos introduzidos na versão da API 2018-09-15-preview. – Esta versão inclui suporte pata:
    - [Domínio e tópicos de domínio](event-domains.md)
    - Introdução à [data de validade para a assinatura de evento](concepts.md#event-subscription-expiration)
    - Introdução da [filtragem avançada](event-filtering.md#advanced-filtering) para assinaturas de evento
    - A versão estável do SDK direcionada para a versão da API `2018-01-01` continua existindo como a versão 1.3.0

## <a name="next-steps"></a>Próximas etapas