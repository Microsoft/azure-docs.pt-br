---
title: Rotas de eventos
titleSuffix: Azure Digital Twins
description: Entenda como rotear eventos dentro do Azure digital gêmeos e para outros serviços do Azure.
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c51f8c894c9a88b6ae81460623eec616d29b62ff
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050508"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Rotear eventos dentro e fora do Azure digital gêmeos

O gêmeos digital do Azure usa **rotas de eventos** para enviar dados para consumidores fora do serviço. 

Há dois casos principais para enviar dados do gêmeos digital do Azure:
* Envio de dados de um gêmeos no grafo digital do Azure para outro. Por exemplo, quando uma propriedade em uma cópia digital é alterada, convém notificar e atualizar outra cópia digital de maneira adequada.
* Envio de dados para serviços de dados downstream para armazenamento ou processamento adicional (também conhecido como *egresso de dados*). Por exemplo,
  - Um hospital pode querer enviar dados de evento de gêmeos digital do Azure para [Time Series insights (TSI)](../time-series-insights/overview-what-is-tsi.md), para registrar dados de série temporal de eventos relacionados a Handwashing para análise em massa.
  - Uma empresa que já esteja usando [mapas do Azure](../azure-maps/about-azure-maps.md) pode querer usar o gêmeos digital do Azure para aprimorar sua solução. Eles podem habilitar rapidamente um mapa do Azure depois de configurar o Azure digital gêmeos, colocar as entidades do Azure MAP no gêmeos digital do Azure como [gêmeos digital](concepts-twins-graph.md) no grafo de entrelaçamento ou executar consultas poderosas que aproveitam seus mapas do Azure e os dados do Azure digital gêmeos juntos.

As rotas de eventos são usadas para ambos os cenários.

## <a name="about-event-routes"></a>Sobre as rotas de eventos

Uma rota de eventos permite que você envie dados de eventos do digital gêmeos no Azure digital gêmeos para pontos de extremidade definidos personalizados em suas assinaturas. No momento, há suporte para três serviços do Azure para pontos de extremidade: [Hub de eventos](../event-hubs/event-hubs-about.md), [grade de eventos](../event-grid/overview.md)e barramento de [serviço](../service-bus-messaging/service-bus-messaging-overview.md). Cada um desses serviços do Azure pode ser conectado a outros serviços e atua como o middleware, enviando dados para os destinos finais, como TSI ou Azure Maps para qualquer processamento necessário.

O diagrama a seguir ilustra o fluxo de dados de evento por meio de uma solução de IoT maior com um aspecto de gêmeos digital do Azure:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="O Azure digital gêmeos encaminha dados por meio de pontos de extremidade para vários serviços downstream" border="false":::

Os destinos de downstream típicos para rotas de eventos são recursos como o TSI, mapas do Azure, armazenamento e soluções de análise.

### <a name="event-routes-for-internal-digital-twin-events"></a>Rotas de eventos para eventos digitais de entrelaçamento digital

As rotas de eventos também são usadas para lidar com eventos no grafo de entrelaçamento e enviar dados de uma teledigital para o digital. Isso é feito por meio da conexão de rotas de eventos por meio da grade de eventos para computar recursos, como [Azure Functions](../azure-functions/functions-overview.md). Essas funções definem como o gêmeos deve receber e responder a eventos. 

Quando um recurso de computação deseja modificar o gráfico de entrelaçamento com base em um evento recebido por meio da rota de eventos, é útil saber qual é o que precisa ser modificado antes do tempo. 

Como alternativa, a mensagem de evento também contém a ID da fonte de mensagens que enviou a mensagem, de modo que o recurso de computação pode usar consultas ou percorrer relações para localizar um entrelaçamento de destino para a operação desejada. 

O recurso de computação também precisa estabelecer permissões de segurança e de acesso de forma independente.

Para percorrer o processo de configuração de uma função do Azure para processar eventos de alta segurança digital, consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Criar um ponto de extremidade

Para definir uma rota de evento, primeiro os desenvolvedores precisam definir pontos de extremidade. Um **ponto de extremidade** é um destino fora do Azure digital gêmeos que dá suporte a uma conexão de rota. Os destinos com suporte incluem:
* Tópicos personalizados da grade de eventos
* Hub de evento
* Barramento de Serviço

Para criar um ponto de extremidade, você pode usar as APIs REST do Azure digital gêmeos [, comandos da CLI](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)ou o [portal do Azure](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Ao definir um ponto de extremidade, você precisará fornecer:
* O nome do ponto de extremidade
* O tipo de ponto de extremidade (grade de eventos, Hub de eventos ou barramento de serviço)
* A cadeia de conexão primária e a cadeia de conexão secundária para autenticar 
* O caminho do tópico do ponto de extremidade, como *Your-topic.westus2.eventgrid.Azure.net*

As APIs de ponto de extremidade que estão disponíveis no plano de controle são:
* Criar ponto de extremidade
* Obter lista de pontos de extremidade
* Obter ponto de extremidade por nome
* Excluir ponto de extremidade por nome

## <a name="create-an-event-route"></a>Criar uma rota de eventos
 
Para criar uma rota de evento, você pode usar as APIs REST do Azure digital gêmeos [, comandos da CLI](how-to-manage-routes-apis-cli.md#create-an-event-route)ou o [portal do Azure](how-to-manage-routes-portal.md#create-an-event-route).

Aqui está um exemplo de criação de uma rota de eventos dentro de um aplicativo cliente, usando a chamada do `CreateOrReplaceEventRouteAsync` [SDK do .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) : 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. Primeiro, um `DigitalTwinsEventRoute` objeto é criado e o construtor usa o nome de um ponto de extremidade. Este `endpointName` campo identifica um ponto de extremidade como um hub de eventos, uma grade de eventos ou um barramento de serviço. Esses pontos de extremidade devem ser criados em sua assinatura e anexados ao Azure digital gêmeos usando as APIs do plano de controle antes de fazer essa chamada de registro.

2. O objeto de rota de evento também tem um campo de [**filtro**](how-to-manage-routes-apis-cli.md#filter-events) , que pode ser usado para restringir os tipos de eventos que seguem essa rota. Um filtro de `true` habilita a rota sem filtragem adicional (um filtro de `false` desabilita a rota). 

3. Esse objeto de rota de evento é passado para `CreateOrReplaceEventRouteAsync` , junto com um nome para a rota.

> [!TIP]
> Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

## <a name="dead-letter-events"></a>Eventos de mensagens mortas

Quando um ponto de extremidade não pode entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um determinado número de vezes, ele pode enviar o evento não entregue para uma conta de armazenamento. Esse processo é conhecido como **mensagens mortas**. O gêmeos digital do Azure enviará um evento inativo quando **uma das condições a seguir** for atendida. 

* O evento não é entregue dentro do período de vida útil
* O número de tentativas para entregar o evento excedeu o limite

Se qualquer uma das condições for atendida, o evento será descartado ou inativo. Por padrão, cada ponto de extremidade **não** ativa as mensagens mortas. Para habilitá-lo, você deve especificar uma conta de armazenamento para manter os eventos não entregues ao criar o ponto de extremidade. Em seguida, você pode efetuar pull de eventos dessa conta de armazenamento para resolver as entregas.

Antes de configurar o local de mensagens mortas, você deve ter uma conta de armazenamento com um contêiner. Você fornece a URL para esse contêiner ao criar o ponto de extremidade. A letra de inatividade é fornecida como uma URL de contêiner com um token SAS. Esse token precisa apenas `write` de permissão para o contêiner de destino dentro da conta de armazenamento. A URL totalmente formada estará no formato de: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Para saber mais sobre tokens SAS, confira: [ *conceder acesso limitado aos recursos de armazenamento do Azure usando assinaturas de acesso compartilhado (SAS)*](../storage/common/storage-sas-overview.md)

Para saber como configurar um ponto de extremidade com mensagens mortas, consulte [*como gerenciar pontos de extremidades e rotas no gêmeos digital do Azure (APIs e CLI)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Tipos de mensagens de evento

Tipos diferentes de eventos no Hub IoT e no Azure digital gêmeos produzem diferentes tipos de mensagens de notificação, conforme descrito abaixo.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Próximas etapas

Consulte como configurar e gerenciar uma rota de eventos:
* [*Como: gerenciar pontos de extremidade e rotas*](how-to-manage-routes-apis-cli.md)

Ou, consulte como usar Azure Functions para rotear eventos no Azure digital gêmeos:
* [*Como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md)