---
title: Streaming de origens de evento de ingestão – Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o streaming de dados no Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4e22d93d3037c190193f53b7cfdbc87cff2da6ed
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504389"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Origens de evento do Azure Time Series Insights Gen2

O ambiente do Azure Time Series Insights Gen2 pode ter até duas origens de eventos de streaming. Há suporte para dois tipos de recursos do Azure como entradas:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Hubs de eventos do Azure](../event-hubs/event-hubs-about.md)

Os eventos devem ser enviados como JSON codificado em UTF-8.

## <a name="create-or-edit-event-sources"></a>Criar ou editar origens de evento

A origem do evento é o link entre o hub e o ambiente do Azure Time Series Insights Gen2, e um recurso separado do tipo `Time Series Insights event source` é criado no grupo de recursos. O Hub IoT ou os recursos do Hub de Eventos podem residir na mesma assinatura do Azure que o ambiente do Azure Time Series Insights Gen2 ou em uma assinatura diferente. No entanto, é uma prática recomendada alojar o ambiente do Azure Time Series Insights e o Hub IoT ou Hub de Eventos na mesma região do Azure.

Você pode usar o [portal do Azure](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), a [CLI do Azure](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source), [modelos do Azure Resource Manager](time-series-insights-manage-resources-using-azure-resource-manager-template.md) e a [API REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) para criar, editar ou remover as origens de evento do ambiente.

## <a name="start-options"></a>Opções de inicialização

Ao criar uma origem de evento, você tem a opção de especificar quais dados preexistentes devem ser coletados. Essa configuração é opcional. As seguintes opções estão disponíveis:

| Nome   |  Descrição  |  Exemplo de modelo do Azure Resource Manager |
|----------|-------------|------|
| EarliestAvailable | Ingerir todos os dados preexistentes armazenados no IoT ou Hub de Eventos | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Começar a ingerir os dados que chegam depois da criação da origem do evento. Os dados preexistentes transmitidos antes da criação da origem do evento serão ignorados. Essa é a configuração padrão no portal do Azure   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | O ambiente ingerirá dados a partir do horário de enfileiramento personalizado (UTC). Todos os eventos que foram enfileirados no IoT ou Hub de Eventos no horário do enfileiramento personalizado ou depois dele serão ingeridos e armazenados. Todos os eventos que chegaram antes do horário de enfileiramento personalizado serão ignorados. Observe que "tempo de enfileiramento" é o horário (em UTC) quando o evento chegou ao IoT ou Hub de Eventos. Isso é diferente de uma [propriedade de carimbo de data/hora](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) personalizada que esteja dentro do corpo do evento. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Caso você selecione EarliestAvailable e haja muitos dados preexistentes, talvez haja alta latência inicial, pois o ambiente do Azure Time Series Insights Gen2 processa todos os dados.
> - Essa alta latência acaba diminuindo conforme os dados são indexados. Envie um tíquete de suporte pelo portal do Azure se você tiver uma alta latência contínua.

* EarliestAvailable

![Diagrama de EarliestAvailable](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![Diagrama de EventSourceCreationTime](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![Diagrama de CustomEnqueuedTime](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Melhores práticas de ingestão de streaming

- Sempre crie um grupo de consumidores exclusivo para o ambiente do Azure Time Series Insights Gen2 para consumir dados da origem do evento. A reutilização de grupos de consumidores pode causar desconexões aleatórias e resultar em perda de dados.

- Configure o ambiente do Azure Time Series Insights Gen2 e o Hub IoT e/ou Hubs de Eventos na mesma região do Azure. Embora seja possível configurar uma origem de evento em uma região separada, esse cenário não tem suporte e não podemos garantir a alta disponibilidade.

- Não ultrapasse o [limite de taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente ou por limite de partição.

- Configure um [alerta](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) de latência para receber uma notificação caso o seu ambiente enfrente problemas de processamento de dados. Veja as condições de alerta sugeridas em [Cargas de trabalho de produção](./concepts-streaming-ingestion-event-sources.md#production-workloads) abaixo.

- Use a ingestão de streaming somente para dados quase em tempo real e recentes, não há suporte para streaming de dados históricos.

- Entenda como as propriedades são escapadas e como [dados JSON são nivelados e armazenados.](./concepts-json-flattening-escaping-rules.md)

- Siga o princípio de privilégios mínimos ao fornecer cadeias de conexão de origem de evento. Para os Hubs de Eventos, configure uma política de acesso compartilhado somente com a declaração *enviar* e, para o Hub IoT, use somente a permissão de *conexão de serviço*.

> [!CAUTION]
> Se você excluir o Hub IoT ou o Hub de Eventos e recriar um recurso com o mesmo nome, será necessário criar uma origem do evento e anexar o novo Hub IoT ou Hub de Eventos. Os dados não serão ingeridos até que você conclua essa etapa.

## <a name="production-workloads"></a>Cargas de trabalho de produção

Além das práticas recomendadas acima, recomendamos que você implemente o seguinte para cargas de trabalho comercialmente críticas.

- Aumente o tempo de retenção de dados do Hub IoT ou Hub de Eventos para o máximo de sete dias.

- Crie alertas de ambiente no portal do Azure. Os alertas baseados em [métricas](./how-to-monitor-tsi-reference.md#metrics) de plataforma permitem validar o comportamento do pipeline de ponta a ponta. As instruções para criar e gerenciar alertas estão [aqui](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts). Condições de alerta sugeridas:

  - IngressReceivedMessagesTimeLag é maior que 5 minutos
  - IngressReceivedBytes é 0
- Mantenha a carga de ingestão equilibrada entre as partições do Hub IoT ou do Hub de Eventos.

### <a name="historical-data-ingestion"></a>Ingestão de dados históricos

No momento, o uso do pipeline de streaming para importar dados históricos não tem suporte no Azure Time Series Insights Gen2. Se você precisar importar dados passados para o seu ambiente, siga as diretrizes abaixo:

- Não transmita dados dinâmicos e históricos em paralelo. A ingestão de dados fora de ordem resultará em um desempenho de consulta degradado.
- Ingira dados históricos ordenados por tempo para obter um melhor desempenho.
- Fique dentro dos limites da taxa de transferência de ingestão abaixo.
- Desabilite o Armazenamento Warm se os dados forem mais antigos do que o período de retenção do Armazenamento Warm.

## <a name="event-source-timestamp"></a>Carimbo de data/hora de origens de evento

Ao configurar uma origem de evento, você deve informar uma propriedade de ID de carimbo de data/hora. A propriedade do carimbo de data/hora é usada para acompanhar eventos ao longo do tempo. Essa é a hora que será usada como carimbo de data/hora `$ts` nas [APIs de consulta](/rest/api/time-series-insights/dataaccessgen2/query/execute) e na série de plotagem no Gerenciador do Azure Time Series Insights. Se a propriedade do carimbo de data/hora não for informada no momento da criação ou estiver faltando em um evento, a hora de enfileiramento do Hub IoT ou dos Hubs de Evento será usada como padrão. Os valores da propriedade de carimbo de data/hora são armazenados em UTC.

Em geral, os usuários optam por personalizar a propriedade de carimbo de data/hora e usar a hora em que o sensor ou a marca gerou a leitura, em vez de usar o padrão da hora de enfileiramento do hub. Isso é particularmente necessário quando os dispositivos têm perda de conectividade intermitente e um lote de mensagens atrasadas é encaminhado para o Azure Time Series Insights Gen2.

Se o carimbo de data/hora personalizado estiver dentro de um objeto JSON aninhado ou de uma matriz, você precisará informar o nome correto da propriedade seguindo as [convenções de nomenclatura de nivelamento e de escape](concepts-json-flattening-escaping-rules.md). Por exemplo, o carimbo de data/hora de origem de evento do conteúdo JSON mostrado [aqui](concepts-json-flattening-escaping-rules.md#example-a) deve ser inserido como `"values.time"`.

### <a name="time-zone-offsets"></a>Deslocamentos de fuso horário

Os carimbos de data/hora devem ser enviados no formato ISO 8601 e são armazenados em UTC. Se um deslocamento de fuso horário for fornecido, ele será aplicado, e o horário será armazenado e retornado no formato UTC. Se a formatação do deslocamento estiver incorreta, ele será ignorado. Em situações em que a solução não tem o contexto do deslocamento original, você pode enviar os dados de deslocamento em outra propriedade de evento separada a fim de preservar o deslocamento e garantir que o aplicativo possa fazer referência a ele ao responder consultas.

O deslocamento de fuso horário deve ter uma destas formatações:

±HHMMZ</br>
±HH:MM</br>
±HH:MMZ</br>

## <a name="next-steps"></a>Próximas etapas

- Leia as [Regras de escape e nivelamento JSON](./concepts-json-flattening-escaping-rules.md) para entender como os eventos são armazenados.

- Entenda as limitações de [taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do ambiente
