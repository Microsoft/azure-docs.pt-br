---
title: Padrões de solução do Azure Stream Analytics
description: Saiba mais sobre os padrões comuns de solução para Azure Stream Analytics, como painel, mensagens de evento, armazenamentos de dados, enriquecimento de dados de referência e monitoramento.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020308"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Padrões de solução do Azure Stream Analytics

Como muitos outros serviços no Azure, a Stream Analytics é melhor usada com outros serviços para criar uma solução de ponta a ponta maior. Este artigo discute soluções simples de Azure Stream Analytics e vários padrões de arquitetura. Você pode criar esses padrões para desenvolver soluções mais complexas. Os padrões descritos neste artigo podem ser usados em uma ampla variedade de cenários. Exemplos de padrões específicos do cenário estão disponíveis em [arquiteturas de solução do Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Crie um trabalho de Stream Analytics para a experiência de criação de painéis em tempo real

Com o Azure Stream Analytics, você pode rapidamente representar painéis e alertas em tempo real. Uma solução simples ingere eventos de hubs de eventos ou Hub IoT e [alimenta o painel de Power bi com um conjunto de dados de streaming](/power-bi/service-real-time-streaming). Para obter mais informações, consulte o tutorial detalhado [analisar dados de chamada fraudulentas com Stream Analytics e Visualizar resultados no painel Power bi](stream-analytics-real-time-fraud-detection.md).

![Painel Power BI do ASA](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

Essa solução pode ser criada em apenas alguns minutos a partir de portal do Azure. Não há codificação extensa envolvida e a linguagem SQL é usada para expressar a lógica de negócios.

Esse padrão de solução oferece a menor latência da origem do evento para o painel Power BI em um navegador. Azure Stream Analytics é o único serviço do Azure com esse recurso interno.

## <a name="use-sql-for-dashboard"></a>Usar SQL para o painel

O painel de Power BI oferece baixa latência, mas não pode ser usado para produzir relatórios de Power BIs completos. Um padrão de relatório comum é gerar os dados primeiro para o banco de dado SQL. Em seguida, use o conector do SQL do Power BI para consultar o SQL para os dados mais recentes.

![Painel SQL do ASA](media/stream-analytics-solution-patterns/sql-dashboard.png)

O uso do banco de dados SQL proporciona mais flexibilidade, mas às custas de uma latência um pouco mais alta. Essa solução é ideal para trabalhos com requisitos de latência maiores que um segundo. Com esse método, você pode maximizar Power BI recursos para fatiar e refinar os dados para relatórios e muito mais opções de visualização. Você também obterá a flexibilidade de usar outras soluções de painel, como tableau.

O SQL não é um armazenamento de dados de alta taxa de transferência. Atualmente, a taxa de transferência máxima para o banco de dados SQL de Azure Stream Analytics está em cerca de 24 MB/s. Se as origens do evento em sua solução produzirem dados a uma taxa mais alta, você precisará usar a lógica de processamento no Stream Analytics para reduzir a taxa de saída para o SQL. Técnicas como filtragem, agregações em janela, correspondência de padrões com junções temporais e funções analíticas podem ser usadas. A taxa de saída para o SQL pode ser otimizada ainda mais usando técnicas descritas em [Azure Stream Analytics saída para o banco de dados SQL do Azure](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorpore informações em tempo real em seu aplicativo com mensagens de evento

O segundo uso mais popular de Stream Analytics é gerar alertas em tempo real. Nesse padrão de solução, a lógica de negócios no Stream Analytics pode ser usada para detectar padrões ou [anomalias](stream-analytics-machine-learning-anomaly-detection.md) [temporais e espaciais e](stream-analytics-geospatial-functions.md) , em seguida, produzir sinais de alerta. No entanto, ao contrário da solução de painel em que Stream Analytics usa Power BI como um ponto de extremidade preferencial, vários coletores de dados intermediários podem ser usados. Esses coletores incluem hubs de eventos, barramento de serviço e Azure Functions. Você, como o Application Builder, precisa decidir qual coletor de dados funciona melhor para seu cenário.

A lógica do consumidor do evento downstream deve ser implementada para gerar alertas em seu fluxo de trabalho de negócios existente. Como você pode implementar uma lógica personalizada no Azure Functions, Azure Functions é a maneira mais rápida de executar essa integração. Um tutorial para usar o Azure function como a saída de um trabalho de Stream Analytics pode ser encontrado em [executar Azure Functions de trabalhos de Azure Stream Analytics](stream-analytics-with-azure-functions.md). O Azure Functions também dá suporte a vários tipos de notificações, incluindo texto e email. O aplicativo lógico também pode ser usado para essa integração, com hubs de eventos entre Stream Analytics e aplicativo lógico.

![Aplicativo de mensagens de evento ASA](media/stream-analytics-solution-patterns/event-messaging-app.png)

Os hubs de eventos, por outro lado, oferecem o ponto de integração mais flexível. Muitos outros serviços, como o Azure Data Explorer e Time Series Insights podem consumir eventos dos hubs de eventos. Os serviços podem ser conectados diretamente ao coletor de hubs de eventos do Azure Stream Analytics para concluir a solução. Os hubs de eventos também são o mais alto agente de mensagens de taxa de transferência disponível no Azure para esses cenários de integração.

## <a name="dynamic-applications-and-websites"></a>Aplicativos e sites dinâmicos

Você pode criar visualizações em tempo real personalizadas, como Dashboard ou visualização de mapa, usando Azure Stream Analytics e o serviço de Signaler do Azure. Usando o Signalr, os clientes Web podem ser atualizados e mostrar o conteúdo dinâmico em tempo real.

![Aplicativo do ASA dinâmico](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorpore informações em tempo real em seu aplicativo por meio de armazenamentos de dados

A maioria dos Web Services e aplicativos Web hoje usam um padrão de solicitação/resposta para atender à camada de apresentação. O padrão de solicitação/resposta é simples de criar e pode ser facilmente dimensionado com um baixo tempo de resposta usando um front-end sem estado e armazenamentos escalonáveis, como Cosmos DB.

O alto volume de dados geralmente cria afunilamentos de desempenho em um sistema baseado em CRUD. O [padrão de solução de fornecimento de eventos](/azure/architecture/patterns/event-sourcing) é usado para resolver os gargalos de desempenho. Padrões e informações temporais também são difíceis e ineficientes de extrair de um armazenamento de dados tradicional. Aplicativos modernos de alto volume orientados a dados geralmente adotam uma arquitetura baseada em fluxo. Azure Stream Analytics como o mecanismo de computação para dados em movimento é um pivô nessa arquitetura.

![Aplicativo de fornecimento de eventos ASA](media/stream-analytics-solution-patterns/event-sourcing-app.png)

Nesse padrão de solução, os eventos são processados e agregados em armazenamentos de dados por Azure Stream Analytics. A camada de aplicativo interage com os armazenamentos de dados usando o padrão de solicitação/resposta tradicional. Devido à capacidade do Stream Analytics de processar um grande número de eventos em tempo real, o aplicativo é altamente escalonável sem a necessidade de aumentar a camada do armazenamento de dados. A camada do repositório de dados é essencialmente uma exibição materializada no sistema. [Azure Stream Analytics saída para Azure Cosmos DB](stream-analytics-documentdb-output.md) descreve como Cosmos DB é usado como uma saída de Stream Analytics.

Em aplicativos reais em que a lógica de processamento é complexa e há a necessidade de atualizar determinadas partes da lógica de forma independente, vários trabalhos de Stream Analytics podem ser compostos junto com os hubs de eventos como o agente de evento intermediário.

![Aplicativo de fornecimento de evento complexo ASA](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

Esse padrão melhora a resiliência e a capacidade de gerenciamento do sistema. No entanto, embora Stream Analytics garanta exatamente o processamento, há uma pequena chance de que os eventos duplicados possam chegar aos hubs de eventos intermediários. É importante para o trabalho de Stream Analytics downstream desduplicar eventos usando chaves lógicas em uma janela lookback. Para obter mais informações sobre a entrega de eventos, consulte referência de [garantias de entrega de eventos](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) .

## <a name="use-reference-data-for-application-customization"></a>Usar dados de referência para personalização do aplicativo

O Azure Stream Analytics recurso de dados de referência foi projetado especificamente para personalização do usuário final, como limite de alerta, regras de processamento e [limites](geospatial-scenarios.md)geográficos. A camada de aplicativo pode aceitar alterações de parâmetro e armazená-las no banco de dados SQL. O trabalho de Stream Analytics consulta periodicamente em busca de alterações do banco de dados e torna os parâmetros de personalização acessíveis por meio de uma junção de dado de referência. Para obter mais informações sobre como usar dados de referência para personalização de aplicativos, consulte [dados de referência do SQL](sql-reference-data.md) e junção de dados de [referência](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Esse padrão também pode ser usado para implementar um mecanismo de regras em que os limites das regras são definidos a partir dos dados de referência. Para obter mais informações sobre regras, consulte [regras baseadas em limites de processos configuráveis no Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![Aplicativo de dados de referência do ASA](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Adicione Machine Learning às suas ideias em tempo real

Azure Stream Analytics modelo de detecção de [anomalias](stream-analytics-machine-learning-anomaly-detection.md) interno é uma maneira conveniente de introduzir Machine Learning ao seu aplicativo em tempo real. Para uma maior variedade de necessidades de Machine Learning, consulte [Azure Stream Analytics integra-se ao serviço de Pontuação de Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Para usuários avançados que desejam incorporar o treinamento online e a pontuação no mesmo pipeline de Stream Analytics, consulte este exemplo de como fazer isso com a [regressão linear](stream-analytics-high-frequency-trading.md).

![ASA Machine Learning aplicativo](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>Data Warehousing em tempo real

Outro padrão comum é o data warehousing em tempo real, também chamado de streaming data warehouse. Além dos eventos que chegam aos hubs de eventos e ao Hub IoT do seu aplicativo, [Azure Stream Analytics em execução em IOT Edge](stream-analytics-edge.md) podem ser usados para atender à limpeza de dados, à redução de dados e às necessidades de encaminhamento e armazenamento de dados. Stream Analytics em execução em IoT Edge pode controlar normalmente a limitação da largura de banda e os problemas de conectividade no sistema. Stream Analytics pode dar suporte a taxas de taxa de transferência de até 200 MB/s ao gravar no Azure Synapse Analytics.

![Data Warehousing do ASA](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>Arquivando dados em tempo real para análise

A maioria das atividades de ciência de dados e análise ainda ocorrem offline. Os dados podem ser arquivados por Azure Stream Analytics por meio de Azure Data Lake Store formatos de saída Gen2 e saída de parquet. Esse recurso remove o conflito de alimentar dados diretamente em Azure Data Lake Analytics, Azure Databricks e Azure HDInsight. Azure Stream Analytics é usado como um mecanismo de ETL quase em tempo real nesta solução. Você pode explorar dados arquivados em Data Lake usando vários mecanismos de computação.

> [!div class="mx-imgBorder"]
> ![Análise offline do ASA](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>Usar dados de referência para enriquecimento

O enriquecimento de dados geralmente é um requisito para os mecanismos de ETL. O Azure Stream Analytics dá suporte ao enriquecimento de dados com [dados de referência](stream-analytics-use-reference-data.md) do banco do dados SQL e do armazenamento de BLOBs do Azure. O enriquecimento de dados pode ser feito para a aterrissagem de dados no Azure Data Lake e no Azure Synapse Analytics.


![Análise offline de ASA com enriquecimento de dados](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacionalização de informações de dados arquivados

Se você combinar o padrão de análise offline com o padrão de aplicativo quase em tempo real, poderá criar um loop de comentários. O loop de comentários permite que o aplicativo se ajuste automaticamente para padrões de alteração nos dados. Esse loop de comentários pode ser tão simples quanto alterar o valor do limite para alertas ou tão complexo quanto treinar novamente Machine Learning modelos. A mesma arquitetura de solução pode ser aplicada a ambos os trabalhos do ASA em execução na nuvem e em IoT Edge.

![Operacionalização do ASA insights](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Como monitorar trabalhos do ASA

Um trabalho de Azure Stream Analytics pode ser executado 24/7 para processar eventos de entrada continuamente em tempo real. Sua garantia de tempo de atividade é crucial para a integridade do aplicativo geral. Embora Stream Analytics seja o único serviço de análise de streaming no setor que oferece uma  [garantia de disponibilidade de 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), você ainda poderá incorrer em algum nível de tempo de inatividade. Ao longo dos anos, Stream Analytics introduziu métricas, logs e Estados de trabalho para refletir a integridade dos trabalhos. Todos eles são exibidos por meio do serviço Azure Monitor e podem ser exportados para o OMS. Para obter mais informações, consulte [entender Stream Analytics monitoramento de trabalho e como monitorar consultas](stream-analytics-monitoring.md).

![Monitoramento do ASA](media/stream-analytics-solution-patterns/monitoring.png)

Há duas coisas principais a serem monitoradas:

- [Estado de falha do trabalho](job-states.md)

    Primeiro, você precisa verificar se o trabalho está em execução. Sem o trabalho no estado de execução, nenhuma nova métrica ou log é gerado. Os trabalhos podem mudar para um estado de falha por vários motivos, incluindo ter um alto nível de utilização de SU (ou seja, ficando sem recursos).

- [Métricas de atraso de marca d' água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Essa métrica reflete o quanto o seu pipeline de processamento está no tempo do relógio (segundos). Alguns dos atrasos são atribuídos à lógica de processamento inerente. Como resultado, o monitoramento da tendência crescente é muito mais importante do que o monitoramento do valor absoluto. O atraso de estado estacionário deve ser resolvido pelo design do aplicativo, não pelo monitoramento ou alertas.

Após a falha, os logs de atividades e os [logs de diagnóstico](stream-analytics-job-diagnostic-logs.md) são os melhores lugares para começar a procurar erros.

## <a name="build-resilient-and-mission-critical-applications"></a>Crie aplicativos resilientes e de missão crítica

Independentemente da garantia de SLA Azure Stream Analytics e do quão cuidadoso você executa seu aplicativo de ponta a ponta, as interrupções acontecem. Se seu aplicativo for de missão crítica, você precisa estar preparado para interrupções a fim de recuperar-se normalmente.

Para aplicativos de alerta, a coisa mais importante é detectar o próximo alerta. Você pode optar por reiniciar o trabalho a partir da hora atual ao recuperar, ignorando os alertas anteriores. A semântica de hora de início do trabalho é a primeira hora de saída, não a primeira hora de entrada. A entrada é rebobinada para trás uma quantidade apropriada de tempo para garantir que a primeira saída na hora especificada seja concluída e correta. Você não obterá agregações parciais e disparará alertas inesperadamente como resultado.

Você também pode optar por iniciar a saída de alguma quantidade de tempo no passado. Os hubs de eventos e as políticas de retenção do Hub IoT contêm uma quantidade razoável de dados para permitir o processamento do passado. A desvantagem é a rapidez com que você pode se acumular até a hora atual e começar a gerar alertas novos e oportunos. Os dados perdem seu valor rapidamente ao longo do tempo, portanto, é importante acompanhar o tempo atual rapidamente. Há duas maneiras de acompanhar rapidamente:

- Provisione mais recursos (SU) durante a captura.
- Reiniciar a partir da hora atual.

A reinicialização a partir do momento é simples de fazer, com a compensação de deixar uma lacuna durante o processamento. Reiniciar dessa maneira pode estar OK para cenários de alerta, mas pode ser problemático para cenários de painel e é um não-inicial para cenários de arquivamento e data warehouse.

O provisionamento de mais recursos pode acelerar o processo, mas o efeito de ter um surto de taxa de processamento é complexo.

- Teste se seu trabalho é escalonável para um número maior de SUs. Nem todas as consultas são escalonáveis. Você precisa certificar-se de que sua consulta está [paralelizada](stream-analytics-parallelization.md).

- Verifique se há partições suficientes nos hubs de eventos upstream ou no Hub IoT que você pode adicionar mais unidades de taxa de transferência (TUs) para dimensionar a taxa de transferência de entrada. Lembre-se de que cada Hub de eventos TU maximizar a uma taxa de saída de 2 MB/s.

- Verifique se você provisionou recursos suficientes nos coletores de saída (ou seja, banco de dados SQL, Cosmos DB) e, portanto, eles não aceleram o surto na saída, o que às vezes pode fazer com que o sistema seja bloqueado.

A coisa mais importante é antecipar a alteração da taxa de processamento, testar esses cenários antes de entrar em produção e estar pronto para dimensionar o processamento corretamente durante o tempo de recuperação de falha.

No cenário extremo que os eventos de entrada são todos atrasados, é [possível que todos os eventos atrasados sejam descartados](stream-analytics-time-handling.md) se você aplicou uma janela de chegada tardia ao seu trabalho. A remoção dos eventos pode parecer um comportamento misterioso no início; no entanto, considerando Stream Analytics é um mecanismo de processamento em tempo real, espera-se que os eventos de entrada estejam próximos do tempo do relógio do mural. Ele precisa descartar eventos que violam essas restrições.

### <a name="lambda-architectures-or-backfill-process"></a>Arquiteturas lambda ou processo de aterramento

Felizmente, o padrão de arquivamento de dados anterior pode ser usado para processar esses eventos atrasados normalmente. A ideia é que o trabalho de arquivamento processa eventos de entrada em tempo de chegada e arquiva eventos no Bucket de tempo certo no blob do Azure ou Azure Data Lake Store com a hora do evento. Não importa quanto atraso um evento chega, ele nunca será Descartado. Ele sempre ficará no Bucket de tempo certo. Durante a recuperação, é possível reprocessar os eventos arquivados e esaterrar os resultados para o armazenamento de sua escolha. Isso é semelhante à forma como os padrões lambda são implementados.

![Aterramento de ASA](media/stream-analytics-solution-patterns/back-fill.png)

O processo de aterramento deve ser feito com um sistema de processamento em lotes offline, que provavelmente tem um modelo de programação diferente do Azure Stream Analytics. Isso significa que você precisa implementar novamente toda a lógica de processamento.

Para o preenchimento posterior, ainda é importante, pelo menos, provisionar temporariamente mais recursos para os coletores de saída para lidar com uma taxa de transferência maior do que as necessidades de processamento de estado constante.

|Cenários  |Reiniciar somente a partir de agora  |Reiniciar a partir da hora da última interrupção |Reiniciar de agora + aterramento com eventos arquivados|
|---------|---------|---------|---------|
|**Painéis**   |Cria uma lacuna    |OK para uma interrupção curta    |Usar para interrupção longa |
|**Alertas**   |Aceitável |OK para uma interrupção curta    |Não é necessário |
|**Aplicativo de fornecimento de eventos** |Aceitável |OK para uma interrupção curta    |Usar para interrupção longa |
|**Data warehouse**   |Perda de dados  |Aceitável |Não é necessário |
|**Análise offline**  |Perda de dados  |Aceitável |Não é necessário|

## <a name="putting-it-all-together"></a>Juntando as peças

Não é difícil imaginar que todos os padrões de solução mencionados acima podem ser combinados juntos em um sistema complexo de ponta a ponta. O sistema combinado pode incluir painéis, alertas, aplicativos de fornecimento de eventos, data warehousing e recursos de análise offline.

A chave é projetar seu sistema em padrões combináveis, para que cada subsistema possa ser compilado, testado, atualizado e recuperado de forma independente.

## <a name="next-steps"></a>Próximas etapas

Agora você viu uma variedade de padrões de solução usando Azure Stream Analytics. Em seguida, você pode se aprofundar e criar seu primeiro trabalho do Stream Analytics:

* [Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md).
* [Criar um trabalho do Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Criar um trabalho do Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).
