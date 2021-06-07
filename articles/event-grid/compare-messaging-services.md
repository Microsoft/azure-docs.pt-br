---
title: Comparar os serviços de mensagens do Azure
description: Descreve os três serviços de mensagens do Azure – Grade de Eventos do Azure, Hubs de Eventos e Barramento de Serviço. Recomenda que serviço usar para cenários diferentes.
ms.topic: overview
ms.date: 07/07/2020
ms.openlocfilehash: 7a3a0cd7f63a67206053ae55f33bd71aee2c19c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460398"
---
# <a name="choose-between-azure-messaging-services---event-grid-event-hubs-and-service-bus"></a>Escolha entre os três serviços de mensagens do Azure – Grade de Eventos, Hubs de Eventos e Barramento de Serviço

O Azure oferece três serviços lhe ajudam na entrega de mensagens de evento ao longo de uma solução. Esses serviços são:

* [Grade de Eventos](./index.yml)
* [Hubs de Evento](../event-hubs/index.yml)
* [Barramento de Serviço](../service-bus-messaging/index.yml)

Embora tenham algumas semelhanças, cada serviço é criado para cenários específicos. Este artigo descreve as diferenças entre esses serviços e ajuda a entender qual deles escolher para seu aplicativo. Em muitos casos, os serviços de mensagens são complementares e podem ser usados juntos.

## <a name="event-vs-message-services"></a>Serviços de mensagens vs de eventos

Há uma distinção importante a observar entre serviços que entregam um evento e serviços que entregam uma mensagem.

### <a name="event"></a>Evento

Um evento é uma notificação leve de uma condição ou de uma alteração de estado. O editor do evento não tem expectativa sobre como o evento é manipulado. O consumidor do evento decide o que fazer com a notificação. Os eventos podem ser unidades discretas ou parte de uma série.

Eventos discretos relatam alterações de estado e são acionáveis. Para dar continuidade, o consumidor apenas precisa saber o que ocorreu. Os dados do evento têm informações sobre o que aconteceu, mas não têm os dados que dispararam o evento. Por exemplo, um evento notifica os consumidores que um arquivo foi criado. Ele pode ter informações gerais sobre o arquivo, mas não contém o próprio arquivo. Eventos discretos são ideais para soluções [sem servidor](https://azure.com/serverless) que precisam ser dimensionadas.

Eventos de série relatam uma condição e são analisáveis. Os eventos são ordenados por tempo e inter-relacionados. O consumidor precisa da série sequenciada de eventos para analisar o que ocorreu.

### <a name="message"></a>Mensagem

Uma mensagem é composta de dados brutos produzidos por um serviço para serem consumidos ou armazenados em outro lugar. A mensagem contém os dados que dispararam o pipeline de mensagem. O editor da mensagem tem uma expectativa sobre como o consumidor manipula a mensagem. Há um contrato entre os dois lados. Por exemplo, o editor envia uma mensagem com os dados brutos e espera que o consumidor crie um arquivo a partir desses dados e envie uma resposta quando o trabalho for concluído.

## <a name="comparison-of-services"></a>Comparação de serviços

| Serviço | Finalidade | Type | Quando usar |
| ------- | ------- | ---- | ----------- |
| Grade de Eventos | Programação reativa | Distribuição de eventos (discreto) | Reagir a alterações de status |
| Hubs de Eventos | Pipeline de Big Data | Streaming de evento (série) | Telemetria e streaming de dados distribuídos |
| Barramento de Serviço | Mensagens corporativas de alto valor | Mensagem | Processamento de pedidos e transações financeiras |

### <a name="event-grid"></a>Grade de Eventos

A Grade de Eventos é um backplane de eventos que permite a programação reativa e controlada por evento. Ele usa um modelo de publicação/assinatura. Editores emitem eventos, mas não têm nenhuma expectativa sobre quais eventos são manipulados. Os assinantes decidem quais eventos desejam manipular.

A Grade de Eventos está profundamente integrada aos serviços do Azure e pode ser integrada aos serviços de terceiros. Ele simplifica o consumo de eventos e reduz os custos, eliminando a necessidade de sondagem de constante. A Grade de Eventos faz o roteamento de eventos eficiente e confiável e encaminha eventos de recursos do Azure e não Azure. Ela distribui os eventos para os pontos de extremidade do assinante registrado. A mensagem de evento tem as informações necessárias para reagir a alterações em serviços e aplicativos. A Grade de Eventos não é um pipeline de dados e não entrega o objeto real atualizado.

A Grade de Eventos dá suporte a mensagens mortas para eventos que não são entregues a um ponto de extremidade.

Ele tem as seguintes características:

* dinamicamente escalonável
* baixo custo
* sem servidor
* pelo menos uma entrega

### <a name="event-hubs"></a>Hubs de Eventos

O Hubs de Eventos do Azure é um pipeline de Big Data. Ele facilita a captura, a retenção e a reprodução de telemetria e dados de fluxo de eventos. Os dados podem vir de várias fontes simultâneas. O Hubs de Eventos permite que os dados de telemetria e eventos sejam disponibilizados para uma variedade de infraestruturas de processamento de fluxo e serviços de análise. Ele está disponível como fluxos de dados ou lotes de eventos em pacote. Esse serviço fornece uma solução única que permite a recuperação rápida de dados para processamento em tempo real, bem como reprodução repetida de dados brutos armazenados. Ele pode capturar os dados de streaming em um arquivo para processamento e análise.

Ele tem as seguintes características:

* baixa latência
* capaz de receber e processar milhões de eventos por segundo
* pelo menos uma entrega

### <a name="service-bus"></a>Barramento de Serviço

O Barramento de Serviço destina-se a aplicativos empresariais tradicionais. Esses aplicativos empresariais tradicionais exigem transações, ordenação, detecção de duplicidades e consistência instantânea. O Barramento de Serviço permite que os aplicativos [nativos de nuvem](https://azure.microsoft.com/overview/cloudnative/) forneçam gerenciamento de transição de estado confiável para os processos empresariais. Ao manipular mensagens de alto valor que não podem ser perdidas ou duplicadas, use o Barramento de Serviço do Azure. O Barramento de Serviço também facilita a comunicação altamente segura em soluções de nuvem híbrida e pode se conectar a sistemas locais existentes para soluções de nuvem.

O Barramento de Serviço é um sistema de mensagens agenciado. Ele armazena mensagens de maneira confiável em um "agente" (por exemplo, uma fila) até que a parte consumidora esteja pronta para recebê-las.

Ele tem as seguintes características:

* entrega confiável de mensagens assíncronas (sistema de mensagens empresarial como um serviço) que requer sondagem
* recursos de sistema de mensagens avançados como PEPS, envio em lote/sessões, transações, colocação de mensagens mortas em fila, controle temporal, roteamento e filtragem e detecção de duplicatas
* pelo menos uma entrega
* opcional na entrega em ordem

## <a name="use-the-services-together"></a>Usar os serviços juntos

Em alguns casos, você usa os serviços lado a lado para cumprir funções distintas. Por exemplo, um site de comércio eletrônico pode usar o Barramento de Serviço para processar o pedido, o Hubs de Eventos para capturar a telemetria de site e a Grade de Eventos para responder a eventos como o envio de um item.

Em outros casos, você os vincula juntos para formar um pipeline de dados e eventos. Você usa a Grade de Eventos para responder a eventos nos outros serviços. Para obter um exemplo de como usar a Grade de Eventos com o Hubs de Eventos para migrar dados para um data warehouse, consulte [Transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md). A imagem a seguir mostra o fluxo de trabalho para transmitir os dados.

![Visão geral de dados de fluxo](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 
- [Opções de mensagens assíncronas no Azure](/azure/architecture/guide/technology-choices/messaging)
- [Eventos, pontos de dados e mensagens – escolhendo o serviço de mensagens do Azure correto para seus dados](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
- [Filas de armazenamento e filas do Barramento de Serviço — comparações e contrastes](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- Para começar a usar a Grade de Eventos, confira [Criar e rotear eventos personalizados com a Grade de Eventos do Azure](custom-event-quickstart.md).
- Para começar a usar o Hubs de Eventos, consulte [Criar um namespace do Hubs de Eventos e um hub de eventos usando o Portal do Azure](../event-hubs/event-hubs-create.md).
- Para começar a usar o Barramento de Serviço, consulte [Criar um namespace do Barramento de Serviço usando o Portal do Azure](../service-bus-messaging/service-bus-create-namespace-portal.md).
