---
title: Federação de vários sites e várias regiões-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da Federação de vários sites e várias regiões com os hubs de eventos do Azure.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861460"
---
# <a name="multi-site-and-multi-region-federation"></a>Federação de vários sites e várias regiões

Muitas soluções sofisticadas exigem que os mesmos fluxos de eventos sejam disponibilizados para consumo em vários locais ou exigem que os fluxos de eventos sejam coletados em vários locais e, em seguida, consolidados em um local específico para consumo. Também há a necessidade de enriquecer ou reduzir fluxos de eventos ou fazer conversões de formato de evento, também para dentro de uma única região e solução.

Praticamente, isso significa que sua solução manterá vários hubs de eventos, geralmente em diferentes regiões e namespaces de hubs de eventos, e, em seguida, replicará os eventos entre eles. Você também pode trocar eventos com origens e destinos como o [barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md), [Hub IOT do Azure](../iot-fundamentals/iot-introduction.md)ou [Apache Kafka](https://kafka.apache.org). 

A manutenção de vários hubs de eventos ativos em diferentes regiões também permite que os clientes escolham e alternem entre eles se seu conteúdo estiver sendo mesclado, o que torna o sistema geral mais resiliente contra problemas de disponibilidade regional.

Este capítulo "Federação" explica os padrões de Federação e como perceber esses padrões usando o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) sem servidor ou os tempos de execução de [Azure Functions][1] , com a opção de ter seu próprio código de transformação ou de enriquecimento no caminho do fluxo de eventos. 

## <a name="federation-patterns"></a>Padrões de Federação

Há muitas motivações possíveis para o porquê de você querer mover eventos entre diferentes hubs de eventos ou outras origens e destinos, e enumeramos os padrões mais importantes nesta seção e também são vinculados a diretrizes mais detalhadas para o respectivo padrão. 

- [Resiliência contra eventos de disponibilidade regional](#resiliency-against-regional-availability-events)
- [Otimização de latência](#latency-optimization)
- [Validação, redução e enriquecimento](#validation-reduction-and-enrichment)
- [Integração com o Analytics Services](#integration-with-analytics-services)
- [Consolidação e normalização de fluxos de eventos](#consolidation-and-normalization-of-event-streams)
- [Divisão e roteamento de fluxos de eventos](#splitting-and-routing-of-event-streams)
- [Projeções de log](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Resiliência contra eventos de disponibilidade regional 

![Disponibilidade regional](media/event-hubs-federation-overview/regional-availability.jpg)

Embora a disponibilidade e a confiabilidade máximas sejam as principais prioridades operacionais para os hubs de eventos, existem, no entanto, muitas maneiras pelas quais um produtor ou consumidor pode ser impedido de se comunicar com seu hub de eventos "primário" atribuído devido a problemas de resolução de nomes ou de rede, ou onde um hub de eventos pode, de fato, não responder temporariamente ou retornar erros. 

Essas condições não são "desastrosas", de modo que você desejará abandonar a implantação regional, como faria em uma situação de [recuperação de desastre](event-hubs-geo-dr.md) , mas o cenário de negócios de alguns aplicativos pode já estar impactado por eventos de disponibilidade que não durar mais do que alguns minutos ou até mesmo segundos. 

Há dois padrões fundamentais para lidar com esses cenários:

- O padrão de [replicação][4] é sobre a replicação do conteúdo de um hub de eventos primário para um hub de eventos secundário, no qual o Hub de eventos primário geralmente é usado pelo aplicativo para produzir e consumir eventos, e o secundário serve como uma opção de fallback caso o Hub de eventos primário esteja ficando indisponível. Como a replicação é unidirecional, do primário para o secundário, uma comutação de produtores e consumidores de um primário não disponível para o secundário fará com que o primário antigo não receba mais novos eventos e, portanto, não será mais atual.
  A replicação pura, portanto, é adequada apenas para cenários de failover unidirecional. Depois que o failover for executado, o primário antigo será abandonado e um novo hub de eventos secundário precisará ser criado em uma região de destino diferente.
- O padrão de [mesclagem][5] estende o padrão de replicação executando uma mesclagem contínua do conteúdo de dois ou mais hubs de eventos. Cada evento produzido originalmente em um dos hubs de eventos incluídos no esquema é replicado para os outros hubs de eventos. À medida que os eventos são replicados, eles são anotados de forma que sejam ignorados posteriormente pelo processo de replicação do destino de replicação. Os resultados do uso do padrão de mesclagem são dois ou mais hubs de eventos que conterão o mesmo conjunto de eventos de maneira eventualmente consistente. 
  
Em ambos os casos, o conteúdo dos hubs de eventos não será idêntico. Os eventos de qualquer produtor e agrupados pela mesma chave de partição aparecerão na mesma ordem relativa que foi enviada originalmente, mas a ordem absoluta dos eventos pode ser diferente. Isso é especialmente verdadeiro para cenários em que a contagem de partições de hubs de eventos de origem e de destino difere, o que é desejável para vários dos padrões estendidos descritos aqui. Um [divisor ou roteador](#splitting-and-routing-of-event-streams) pode obter uma fatia de um hub de eventos muito maior com centenas de partições e funil em um hub de eventos menor com apenas algumas partições, mais adequadas para manipular o subconjunto com recursos de processamento limitado. Por outro lado, uma [consolidação](#consolidation-and-normalization-of-event-streams) pode fazer o funil de dados de vários hubs de eventos menores em um hub de eventos único e maior com mais partições para lidar com as necessidades consolidadas de taxa de transferência e processamento.

O critério para manter eventos juntos é a chave de partição e não a ID da partição original. Considerações adicionais sobre a ordem relativa e como executar um failover de um hub de eventos para o próximo sem depender do mesmo escopo de deslocamentos de fluxo são discutidas na descrição do padrão de [replicação][4] .


Diretrizes: 
- [Padrão de replicação][4]
- [Padrão de mesclagem][5]

### <a name="latency-optimization"></a>Otimização de latência 

![Otimização de latência](media/event-hubs-federation-overview/latency-optimization.jpg)  

Os fluxos de eventos são gravados uma vez por produtores, mas podem ser lidos várias vezes por consumidores de evento. Para cenários em que um fluxo de eventos em uma região é compartilhado por vários consumidores e precisa ser acessado repetidamente durante o processamento de análise que reside em uma região diferente, ou com as demandas que poderiam trazer para os consumidores simultâneos, pode ser benéfico colocar uma cópia do fluxo de eventos perto do processador de análise para reduzir a latência de ida e volta. 

Bons exemplos de quando a replicação deve ser preferível a consumir eventos remotamente de várias regiões são especialmente aqueles onde as regiões estão extremamente distantes, por exemplo, Europa e Austrália sendo quase Antipodes, geograficamente e as latências de rede podem facilmente exceder 250 MS para qualquer viagem de ida e volta.
Você não pode acelerar a velocidade de luz, mas pode reduzir o número de viagens de ida e volta de alta latência para interagir com os dados.

Diretrizes: 
- [Padrão de replicação][4]

### <a name="validation-reduction-and-enrichment"></a>Validação, redução e enriquecimento

![Validação, redução, enriquecimento](media/event-hubs-federation-overview/validation-enrichment.jpg)  

Os fluxos de eventos podem ser enviados para um hub de eventos por clientes externos à sua própria solução. Esses fluxos de eventos podem exigir que os eventos enviados externamente sejam verificados quanto à conformidade com um determinado esquema e que os eventos sem conformidade sejam removidos. 

Em cenários em que os clientes têm uma largura de banda extremamente restrita, pois é o caso em muitos cenários de "Internet das Coisas" com largura de banda limitada, ou onde os eventos são enviados originalmente por redes não IP com tamanhos de pacotes restritos, os eventos podem ter que ser aprimorados com os dados de referência para adicionar mais contexto para serem utilizáveis por processadores de eventos downstream.

Em outros casos, especialmente quando os fluxos estão sendo consolidados, os dados do evento podem precisar ser reduzidos em complexidade ou em tamanho absoluto, omitindo alguns detalhes.

Qualquer uma dessas operações pode ocorrer como parte da replicação, consolidação ou fluxos de mesclagem. 

Diretrizes: 
- [Padrão do editor][6]

### <a name="integration-with-analytics-services"></a>Integração com o Analytics Services

![Integração com o Analytics Services](media/event-hubs-federation-overview/integration.jpg)

Vários serviços de análise nativa de nuvem do Azure, como Azure Stream Analytics ou Synapse do Azure, funcionam melhor com dados em fluxo ou em lotes servidos dos hubs de eventos do Azure, e os hubs de eventos do Azure também habilitam a integração com vários pacotes de análise de software livre, como Apache Samza, Apache Flink, Apache Spark e Apache Storm. 

Se sua solução usa principalmente o barramento de serviço ou a grade de eventos, você pode tornar esses eventos facilmente acessíveis para esses sistemas de análise e também para arquivamento com a captura de hubs de eventos, se você os direcionar para o Hub de eventos. A grade de eventos pode fazer isso nativamente com sua [integração de Hub de eventos](../event-grid/handler-event-hubs.md), para o barramento de serviço, siga as diretrizes de replicação do barramento de [serviço](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub).

O Azure Stream Analytics [integra-se diretamente com os hubs de eventos](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs).

Diretrizes: 
- [Padrão de replicação][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Consolidação e normalização de fluxos de eventos

![Consolidação e normalização de fluxos de eventos](media/event-hubs-federation-overview/consolidation.jpg)

As soluções globais são geralmente compostas por superfície regionais que são amplamente independentes, incluindo ter seus próprios recursos de análise, mas as perspectivas de análises globais e supras exigirão uma perspectiva integrada e é por isso que uma consolidação central dos mesmos fluxos de eventos são avaliados nos respectivos vestígios regionais para a perspectiva local. 

A normalização é um tipo de cenário de consolidação, no qual dois ou mais fluxos de eventos de entrada carregam o mesmo tipo de evento, mas com estruturas diferentes ou codificações diferentes, e os eventos mais são transcodificados ou transformados antes que possam ser consumidos. 

A normalização também pode incluir trabalho criptográfico, como descriptografar cargas criptografadas de ponta a ponta e criptografá-la novamente com chaves e algoritmos diferentes para o público do consumidor downstream. 

Diretrizes: 
- [Padrão de mesclagem][5]
- [Padrão do editor][6]

### <a name="splitting-and-routing-of-event-streams"></a>Divisão e roteamento de fluxos de eventos

![Divisão e roteamento de fluxos de eventos](media/event-hubs-federation-overview/splitting.jpg)

Os hubs de eventos do Azure são usados ocasionalmente em cenários de estilo "publicar-assinar", em que uma torrent de entrada de eventos ingeridos ultrapassa a capacidade do barramento de serviço do Azure ou da grade de eventos do Azure, ambos com recursos de filtragem e de distribuição nativos de publicação e são preferidos para esse padrão. 

Embora uma verdadeira capacidade de "publicar-assinar" Deixe-a para os assinantes selecionar os eventos desejados, o padrão de divisão tem os eventos de mapa de produtor para partições por um modelo de distribuição predeterminado e consumidores designados e, em seguida, recebem exclusivamente a partição "seu". Com o Hub de eventos armazenando em buffer o tráfego geral, o conteúdo de uma partição específica, representando uma fração do volume de taxa de transferência original, pode ser replicado em uma fila para um consumo de consumidor confiável, transacional e concorrente.

Muitos cenários em que os hubs de eventos são usados principalmente para mover eventos dentro de um aplicativo dentro de uma região têm alguns casos em que os eventos Select, talvez apenas de uma única partição, também precisam ser disponibilizados em outro lugar. Esse cenário é semelhante ao cenário de divisão, mas pode usar um roteador escalonável que considera todas as mensagens que chegam em um hub de eventos e Cherry – escolhe apenas alguns para roteamento em diante e pode diferenciar destinos de roteamento por conteúdo ou metadados de evento. 

Diretrizes:
- [Padrão de roteamento][7]

### <a name="log-projections"></a>Projeções de log 

![Projeção de log](media/event-hubs-federation-overview/log-projection.jpg)

Em alguns cenários, você desejará ter acesso ao valor mais recente enviado para qualquer Subfluxo de um evento e, normalmente, distinguido pela chave de partição. Em Apache Kafka, isso geralmente é obtido com a habilitação de "compactação de log" em um tópico, que descarta tudo, exceto o último evento rotulado com qualquer chave exclusiva. A abordagem de compactação de log tem três desvantagens de composição: 

- A compactação requer uma reorganização contínua do log, que é uma operação excessivamente cara para um agente que é otimizado para cargas de trabalho somente de acréscimo. 
- A compactação é destrutiva e não permite uma perspectiva compactada e não compactada do mesmo fluxo.
- Um fluxo compactado ainda tem um modelo de acesso sequencial, o que significa que encontrar o valor desejado no log requer a leitura de todo o log na pior das hipóteses, o que geralmente leva a otimizações que implementam o padrão exato apresentado aqui: projetando o conteúdo do log em um banco de dados ou cache. 

Por fim, um log compactado é um repositório de chave-valor e, como tal, é a pior opção de implementação possível para tal armazenamento. É muito mais eficiente para pesquisas e consultas para criar e usar uma projeção permanente do log em um repositório de chave-valor adequado ou em algum outro banco de dados. 

Como os eventos são imutáveis e o pedido é sempre preservado em um log, qualquer projeção de um log em um repositório de chave-valor sempre será idêntica ao mesmo intervalo de eventos, o que significa que uma projeção que você mantém atualizado sempre fornece uma exibição autoritativa e nunca há um bom motivo para reconstruí-lo do conteúdo do log uma vez compilado. 

Diretrizes:
- [Projeção de log][8]

## <a name="replication-application-technologies"></a>Tecnologias de aplicativo de replicação

A implementação dos padrões acima requer um ambiente de execução escalonável e confiável para as tarefas de replicação que você deseja configurar e executar. No Azure, os ambientes de tempo de execução mais adequados para essas tarefas são tarefas sem estado [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) para tarefas de replicação de fluxo com estado e [Azure Functions](../azure-functions/functions-overview.md) para tarefas de replicação sem estado.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Aplicativos de replicação com estado no Azure Stream Analytics

Para aplicativos de replicação com estado que precisam considerar relações entre eventos, criar eventos compostos, enriquecer eventos ou reduzir eventos, criar agregações de dados e transformar cargas de evento, [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) é a melhor opção de implementação.

No Azure Stream Analytics, você [cria trabalhos](../stream-analytics/stream-analytics-quick-create-portal.md) que integram [entradas](../stream-analytics/stream-analytics-add-inputs.md) e [saídas](../stream-analytics/stream-analytics-define-outputs.md) e integram os dados das entradas por meio de [consultas](/stream-analytics-query/stream-analytics-query-language-reference) que produzem um resultado que é disponibilizado nas saídas.

As consultas são baseadas na [linguagem de consulta SQL](/stream-analytics-query/stream-analytics-query-language-reference) e podem ser usadas para filtrar, classificar, agregar e unir dados de streaming com facilidade em um período de tempo. Você também pode estender essa linguagem SQL com [](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) as [UDFs (funções definidas pelo usuário) do JavaScript e do C#](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md). Você pode ajustar com facilidade as opções de ordenação de eventos e a duração das janelas de tempo ao executar operações de agregação por meio de simples configurações e/ou constructos de linguagem.

Cada trabalho tem uma ou várias saídas para os dados transformados, e você pode controlar o que acontece em resposta às informações analisadas. Por exemplo, você pode:

- Enviar dados para serviços como Azure Functions, Filas ou Tópicos do Barramento de Serviço para disparar comunicações ou de fluxos de trabalho personalizados downstream.
- Enviar dados a um painel do Power BI para painéis em tempo real.
- Armazene dados em outros serviços de armazenamento do Azure (por exemplo, Azure Data Lake, Azure Synapse Analytics etc.) para executar a análise de lote ou treinar modelos de aprendizado de máquina com base em pools indexados muito grandes de dados históricos.
- Armazenar projeções (também chamadas de "exibições materializadas") em bancos de[dados (banco de dados SQL](../stream-analytics/sql-database-output.md), [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ).

### <a name="stateless-replication-applications-in-azure-functions"></a>Aplicativos de replicação sem estado no Azure Functions

Para tarefas de replicação sem monitoração de estado em que você deseja encaminhar eventos sem considerar suas cargas ou processá-los individualmente sem precisar considerar as relações de eventos (exceto sua ordem relativa), você pode usar Azure Functions, o que proporciona uma enorme flexibilidade.

Azure Functions tem gatilhos pré-criados e escaláveis predefinidos e associações de saída para [hubs de eventos do Azure](../azure-functions/functions-bindings-event-hubs.md), [Hub IOT do Azure](../azure-functions/functions-bindings-event-iot.md), [barramento de serviço do Azure](../azure-functions/functions-bindings-service-bus.md), [grade de eventos do](../azure-functions/functions-bindings-event-grid.md)Azure e [armazenamento de filas do azure](../azure-functions/functions-bindings-storage-queue.md), bem como extensões personalizadas para [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)e [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). A maioria dos gatilhos se adaptará dinamicamente às necessidades de taxa de transferência, dimensionando o número de instâncias em execução simultaneamente com base em métricas documentadas. 

Para criar projeções de log, Azure Functions dá suporte a associações de saída para [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) e [armazenamento de tabelas do Azure](../azure-functions/functions-bindings-storage-table-output.md).

Azure Functions pode ser executado em uma [identidade gerenciada do Azure](../active-directory/managed-identities-azure-resources/overview.md) e com isso, ele pode conter os valores de configuração de credenciais no armazenamento controlado de acesso rigidamente dentro de [Azure Key Vault](../key-vault/general/overview.md).

Além disso, o Azure Functions permite que as tarefas de replicação sejam integradas diretamente às redes virtuais do Azure e aos [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para todos os serviços de mensagens do Azure, e elas estão prontamente integradas ao [Azure monitor](../azure-monitor/overview.md).

Com o plano de consumo de Azure Functions, os gatilhos pré-criados podem até mesmo reduzir para zero, enquanto nenhuma mensagem está disponível para replicação, o que significa que você não incorrerá nenhum custo para manter a configuração pronta para escalar verticalmente; a principal desvantagem de usar o plano de consumo é que a latência das tarefas de replicação "Ativando" desse Estado é significativamente maior do que com os planos de hospedagem em que a infraestrutura é mantida em execução.  

Em contraste com tudo isso, os mecanismos de replicação mais comuns para mensagens e eventos, como o [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) da Apache Kafka exigem que você forneça um ambiente de hospedagem e dimensione o mecanismo de replicação por conta própria. Isso inclui a configuração e a integração dos recursos de segurança e de rede, facilitando o fluxo de dados de monitoramento e, em seguida, você ainda não tem a oportunidade de injetar tarefas de replicação personalizadas no fluxo. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Escolhendo entre Azure Functions e Azure Stream Analytics

Azure Stream Analytics (ASA) é a melhor opção sempre que você precisa processar a carga de seus eventos enquanto os Replica. O ASA pode copiar eventos um por um ou pode criar agregações que condensam as informações de fluxos de eventos antes de encaminhá-lo. Ele pode [prontamente fazer com que você complemente os dados de referência](../stream-analytics/stream-analytics-use-reference-data.md) mantidos no armazenamento de BLOBs do Azure ou no Azure SQL Database sem a necessidade de importar esses dados para um fluxo.

Com o ASA, você pode criar facilmente exibições persistentes e materializadas de fluxos em bancos de dados de hiperescala. É uma abordagem muito superior para o modelo Apache Kafka de "compactação de log" do desajeitado e as projeções de tabela voláteis, do lado do cliente, dos fluxos do Kafka. 

O ASA pode prontamente processar eventos com cargas codificadas nos [formatos CSV, JSON e Apache Avro](../stream-analytics/stream-analytics-parsing-json.md) e você pode conectar [desserializadores personalizados](../stream-analytics/custom-deserializer.md) para qualquer outro formato.

Para todas as tarefas de replicação em que você deseja copiar fluxos de eventos "no estado em que se encontram" e sem tocar nas cargas, ou se precisar implementar um roteador, executar o trabalho de criptografia, alterar a codificação de cargas ou, caso contrário, precisar de controle total sobre o conteúdo do fluxo de dados, Azure Functions é a melhor opção.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, exploramos uma variedade de padrões de Federação e explicamos a função de Azure Functions como o tempo de execução de replicação de evento e de mensagens no Azure.

Em seguida, talvez você queira ler como configurar um aplicativo replicador com Azure Stream Analytics ou Azure Functions e como replicar fluxos de eventos entre os hubs de eventos e vários outros sistemas de mensagens e eventos:

- [Padrões de tarefa de replicação de evento][10]
- [Processar dados com Azure Stream Analytics][9]
- [Aplicativos do replicador de eventos no Azure Functions][1]
- [Replicando eventos entre os hubs de eventos][2]
- [Replicando eventos para o barramento de serviço do Azure][3]
- [Usar Apache Kafka MirrorMaker com hubs de eventos][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md