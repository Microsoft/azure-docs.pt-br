---
title: Publicar e assinar eventos de aplicativo – Grade de Eventos do Azure
description: Envie dados de eventos de uma origem para manipuladores com a Grade de Eventos do Azure. Crie aplicativos baseados em eventos e integre com serviços do Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 05/25/2019
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: b262a06bc3d80bd1f8105c6629bbc07e6ccd34eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077148"
---
# <a name="what-is-azure-event-grid"></a>O que é a Grade de Eventos do Azure?

A Grade de Eventos do Azure permite compilar facilmente aplicativos com as arquiteturas baseadas em evento. Primeiro, selecione o recurso do Azure que você quer assinar e forneça o manipulador de eventos ou o ponto de extremidade do WebHook ao qual enviar o evento. A Grade de Eventos tem suporte interno para eventos provenientes de serviços do Azure, como blobs de armazenamento e grupos de recursos. A Grade de Eventos também tem suporte para seus próprios eventos usando os tópicos personalizados. 

Você pode usar filtros para encaminhar eventos específicos a pontos de extremidade diferentes, multicast para vários pontos de extremidade e verificar se os eventos são distribuídos de forma confiável.

A Grade de Eventos do Azure é implantada para maximizar a disponibilidade distribuindo-se nativamente entre vários domínios de falha em todas as regiões e nas zonas de disponibilidade (em regiões onde há suporte). Atualmente, a Grade de Eventos do Azure está disponível em todas as regiões públicas. Ainda não disponível nas nuvens do Azure Alemanha, Azure na China 21Vianet nem Azure Governamental.

Este artigo fornece uma visão geral da Grade de Eventos do Azure. Se você quiser começar a usar a Grade de Eventos, confira [Criar e rotear eventos personalizados com a Grade de Eventos do Azure](custom-event-quickstart.md). 

![Modelo de Grade de Eventos de origens e manipuladores](./media/overview/functional-model.png)

Esta imagem mostra como a Grade de Eventos conecta origens e manipuladores, mas não é uma lista abrangente de integrações compatíveis.

## <a name="event-sources"></a>Origens de eventos

Para obter detalhes completos sobre os recursos de cada origem, bem como artigos relacionados, confira [origens do evento](event-sources.md). Atualmente, os serviços do Azure a seguir dão suporte para envio de eventos à Grade de Eventos:

* [Assinaturas do Azure (operações de gerenciamento)](event-sources.md#azure-subscriptions)
* [Registro de Contêiner](event-sources.md#container-registry)
* [Tópicos personalizados](event-sources.md#custom-topics)
* [Hubs de Evento](event-sources.md#event-hubs)
* [Hub IoT](event-sources.md#iot-hub)
* [Serviços de Mídia](event-sources.md#media-services)
* [Grupos de recursos (operações de gerenciamento)](event-sources.md#resource-groups)
* [Barramento de Serviço](event-sources.md#service-bus)
* [Armazenamento de Blobs](event-sources.md#storage)
* [Mapas do Azure](event-sources.md#maps)

## <a name="event-handlers"></a>Manipuladores de eventos

Para obter detalhes completos sobre os recursos de cada manipulador, bem como artigos relacionados, confira [manipuladores de evento](event-handlers.md). Atualmente, os serviços do Azure a seguir dão suporte para manipulação de eventos da Grade de Eventos: 

* [Automação do Azure](event-handlers.md#azure-automation)
* [Funções do Azure](event-handlers.md#azure-functions)
* [Hubs de Evento](event-handlers.md#event-hubs)
* [Conexões híbridas](event-handlers.md#hybrid-connections)
* [Aplicativos Lógicos](event-handlers.md#logic-apps)
* [Microsoft Flow](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Armazenamento de Filas](event-handlers.md#queue-storage)
* [Barramento de Serviço](event-handlers.md#service-bus-queue-preview) (Versão prévia)
* [Webhooks](event-handlers.md#webhooks)

## <a name="concepts"></a>Conceitos

Há cinco conceitos na Grade de Eventos do Azure para sua utilização:

* **Eventos**: o que aconteceu.
* **Origens do evento** - Onde o evento ocorreu.
* **Tópicos**: o ponto de extremidade onde os publicadores enviam eventos.
* **Assinaturas de evento**: o ponto de extremidade ou o mecanismo interno para encaminhar eventos, às vezes, para mais de um manipulador. As assinaturas também são usadas por manipuladores para filtrar de forma inteligente os eventos de entrada.
* **Manipuladores de eventos**: o aplicativo ou serviço que reage ao evento.

Para saber mais sobre esses conceitos, confira [Conceitos na Grade de Eventos do Azure](concepts.md).

## <a name="capabilities"></a>Funcionalidades

Aqui estão alguns dos principais recursos da Grade de Eventos do Azure:

* **Simplicidade**: aponte e clique para direcionar eventos de seus recursos do Azure para qualquer manipulador de eventos ou ponto de extremidade.
* **Filtragem avançada**: filtre com tipo de evento ou caminho de publicação do evento para garantir que os manipuladores de eventos recebam apenas eventos relevantes.
* **Fan-out**: assine vários pontos de extremidade no mesmo evento para enviar cópias do evento ao máximo de locais possível.
* **Confiabilidade**: utilize repetição por 24 horas com retirada exponencial para garantir a entrega dos eventos.
* **Pagamento por evento**: pague apenas pela quantidade usada da Grade de Eventos.
* **Alta taxa de transferência**: crie cargas de trabalho de alto volume na Grade de Eventos com suporte para milhões de eventos por segundo.
* **Eventos internos**: comece a executar rapidamente com eventos internos definidos pelo recurso.
* **Eventos personalizados** - use eventos personalizados de rota, filtro e entrega confiável da Grade de Eventos em seu aplicativo.

Para obter uma comparação de Grade de Eventos, Hubs de Eventos e Barramento de Serviço, consulte [Escolher entre os serviços do Azure que entregam mensagens](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>O que posso fazer com a Grade de Eventos?

A Grade de Eventos do Azure fornece vários recursos que melhoram muito o trabalho sem servidor, de automação de operações e de [integração](https://azure.com/integration): 

### <a name="serverless-application-architectures"></a>Arquiteturas de aplicativo sem servidor

![Arquiteturas de aplicativo sem servidor](./media/overview/serverless_web_app.png)

A Grade de Eventos conecta fontes de dados e manipuladores de eventos. Por exemplo, use a Grade de Eventos para disparar uma função sem servidor que analisa imagens quando adicionado a um contêiner de Armazenamento de Blobs. 

### <a name="ops-automation"></a>Automação de operações

![Automação de operações](./media/overview/Ops_automation.png)

A Grade de Eventos permite que você acelere a automação e simplifique a imposição de políticas. Por exemplo, use a Grade de Eventos para notificar a Automação do Azure quando uma máquina virtual ou um Banco de Dados SQL é criado. Use os eventos para verificar automaticamente se as configurações do serviço estão em conformidade, colocar os metadados em ferramentas de operações, marcar máquinas virtuais ou arquivar itens de trabalho.

### <a name="application-integration"></a>Integração de aplicativos

![Integração de aplicativos com o Azure](./media/overview/app_integration.png)

A Grade de Eventos conecta seu aplicativo a outros serviços. Por exemplo, crie um tópico personalizado para enviar os dados de evento do aplicativo para a Grade de Eventos e aproveite a entrega confiável, o roteamento avançado e a integração com o Azure. Como alternativa, você pode usar a Grade de Eventos com os Aplicativos Lógicos para processar dados em qualquer lugar, sem escrever código. 

## <a name="how-much-does-event-grid-cost"></a>Quanto custa a Grade de Eventos?

A Grade de Eventos do Azure usa um modelo de preço de pagamento por evento, para que você pague só pelo que usa. As primeiras 100.000 operações por mês são gratuitas. Operações são definidas como entrada de evento, tentativas de entrega de assinatura, chamadas de gerenciamento e filtragem pelo sufixo do assunto. Para obter detalhes, consulte a [página de preços](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Próximas etapas

* [Eventos de blob de armazenamento de rota](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Responda a eventos de blob de armazenamento usando a Grade de Eventos.
* [Criar e assinar eventos personalizados](custom-event-quickstart.md)  
  Comece imediatamente a enviar seus próprios eventos personalizados para qualquer ponto de extremidade usando o início rápido da Grade de Eventos do Azure.
* [Usando Aplicativos Lógicos como um Manipulador de eventos](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Um tutorial sobre como compilar um aplicativo usando os Aplicativos Lógicos para reagir a eventos enviados por push pela Grade de Eventos.
* [Transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md)  
  Um tutorial que usa o Azure Functions para transmitir dados dos Hubs de Eventos para o SQL Data Warehouse.
* [Referência da API REST da Grade de Eventos](/rest/api/eventgrid)  
  Fornece conteúdo de referência para o gerenciamento de Assinaturas de Eventos, roteamento e filtragem.
