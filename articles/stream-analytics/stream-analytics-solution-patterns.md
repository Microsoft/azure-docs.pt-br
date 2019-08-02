---
title: Padrões de solução de Stream Analytics do Azure
description: Saiba mais sobre padrões de solução para o Azure Stream Analytics.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 5929ff439bc31e16643e5c57868cd6b68f9cd99c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329567"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Padrões de solução de Stream Analytics do Azure

Como muitos outros serviços do Azure, Stream Analytics é melhor usado com outros serviços para criar uma solução de ponta a ponta maior. Este artigo discute vários padrões de arquitetura e de soluções do Azure Stream Analytics simples. Você pode criar esses padrões para desenvolver soluções mais complexas. Os padrões descritos neste artigo podem ser usados em uma ampla variedade de cenários. Exemplos de padrões específicos de cenário estão disponíveis no [arquiteturas de solução do Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Criar um trabalho de Stream Analytics a experiência de criação de painéis em tempo real de energia

Com o Azure Stream Analytics, você pode rapidamente criar painéis em tempo real e alertas. Uma solução simples consome eventos de Hubs de eventos ou IoT Hub, e [feeds do painel do Power BI com um conjunto de dados de streaming](/power-bi/service-real-time-streaming). Para obter mais informações, consulte o tutorial detalhado [analisar dados de chamada telefônica com o Stream Analytics e visualizar os resultados no painel do Power BI](stream-analytics-manage-job.md).

![Painel do ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

Essa solução pode ser criada em apenas alguns minutos do portal do Azure. Não há que nenhuma codificação extensivo envolvido e linguagem SQL é usada para expressar a lógica de negócios.

Esse padrão de solução oferece a menor latência da origem do evento para o painel do Power BI em um navegador. O Azure Stream Analytics é o único serviço do Azure com essa funcionalidade interna.

## <a name="use-sql-for-dashboard"></a>Usar o SQL para o painel

Painel do Power BI oferece baixa latência, mas ele não pode ser usado para produzir relatórios completos do Power BI. É um padrão comum de geração de relatórios para seus dados para um banco de dados SQL de saída pela primeira vez. Em seguida, use o conector SQL do Power BI a consulta SQL para os dados mais recentes.

![Painel SQL ASA](media/stream-analytics-solution-patterns/sqldashboard.png)

Usando o SQL banco de dados oferece mais flexibilidade, mas às custas de uma latência levemente maior. Essa solução é ideal para trabalhos com os requisitos de latência maiores do que um segundo. Com esse método, você pode maximizar recursos do Power BI a fatia adicional e organizar os dados para relatórios e muito mais opções de visualização. Você também ganha a flexibilidade de usar outras soluções do painel, como Tableau.

SQL não é um armazenamento de dados de alta taxa de transferência. A taxa de transferência máxima para um banco de dados SQL do Azure Stream Analytics está atualmente em torno de 24 MB/s. Se as fontes de evento em sua solução gerarem dados em uma taxa mais alta, você precisa usar a lógica de processamento no Stream Analytics para reduzir a taxa de saída para o SQL. Técnicas como filtragem, agregações em janela, correspondência de padrões com junções temporais e funções analíticas podem ser usadas. A taxa de saída para o SQL pode ser ainda mais otimizada usando técnicas descritas [saída do Azure Stream Analytics para o banco de dados do Azure SQL](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorporar informações em tempo real em seu aplicativo com mensagens de eventos

O uso de segundo mais popular do Stream Analytics é gerar alertas em tempo real. Nesse padrão de solução, lógica de negócios no Stream Analytics pode ser usada para detectar [padrões temporais e espaciais](stream-analytics-geospatial-functions.md) ou [anomalias](stream-analytics-machine-learning-anomaly-detection.md), em seguida, gerar sinais de alerta. No entanto, ao contrário da solução do painel onde o Stream Analytics usa o Power BI como um ponto de extremidade preferido, um número de Coletores de dados intermediários pode ser usado. Esses coletores incluem Hubs de eventos, o barramento de serviço e o Azure Functions. Você, como o construtor do aplicativo, precisa decidir quais coletor de dados funciona melhor para seu cenário.

Lógica de consumidor de evento downstream deve ser implementada para gerar alertas em seu fluxo de trabalho de negócios existentes. Como você pode implementar a lógica personalizada no Azure Functions, o Azure Functions é a maneira mais rápida, você pode executar essa integração. Um tutorial sobre como usar a função do Azure como a saída para um trabalho do Stream Analytics pode ser encontrada na [executar Azure Functions de trabalhos do Azure Stream Analytics](stream-analytics-with-azure-functions.md). O Azure Functions também dá suporte a vários tipos de notificações, incluindo texto e email. Aplicativo lógico também pode ser usado para essa integração, com os Hubs de eventos entre o Stream Analytics e o aplicativo lógico.

![Aplicativo de mensagens de evento do ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Hubs de eventos, por outro lado, oferece o ponto de integração mais flexível. Muitos outros serviços, como o Data Explorer do Azure e o Time Series Insights podem consumir eventos de Hubs de eventos. Serviços podem ser conectados diretamente para o coletor de Hubs de eventos do Azure Stream Analytics para concluir a solução. Os Hubs de eventos também é o mais alta taxa de transferência agente de mensagens disponível no Azure para esses cenários de integração.

## <a name="dynamic-applications-and-websites"></a>Sites e aplicativos dinâmicos

Você pode criar visualizações em tempo real personalizadas, como o painel ou visualização, de mapa usando o Azure Stream Analytics e o serviço do Azure SignalR. Usando o SignalR, os clientes da web podem ser atualizados e mostram o conteúdo dinâmico em tempo real.

![Aplicativo dinâmico do ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorporar informações em tempo real em seu aplicativo por meio de armazenamentos de dados

A maioria dos serviços da web e aplicativos web hoje usam um padrão de solicitação/resposta para servir a camada de apresentação. O padrão de solicitação/resposta é simple de criar e podem ser facilmente dimensionados com o tempo de resposta baixos usando um front-end sem monitoração de estado e armazenamentos escalonáveis, como o Cosmos DB.

Alto volume de dados geralmente cria afunilamentos de desempenho em um sistema com base em CRUD. O [padrão de solução de fornecimento de evento](/azure/architecture/patterns/event-sourcing) é usado para resolver os gargalos de desempenho. Informações e padrões temporais também são difíceis e ineficiente para extrair de um armazenamento de dados tradicionais. Dados de alto volume de modernos aplicativos orientados geralmente adotam uma arquitetura baseada em fluxo de dados. A análise do Azure Stream como o mecanismo de computação para dados em movimento é uma base na arquitetura.

![Aplicativo de fornecimento de evento do ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Nesse padrão de solução, os eventos são processados e agregados em armazenamentos de dados pelo Azure Stream Analytics. A camada de aplicativo interage com armazenamentos de dados usando o padrão de solicitação/resposta tradicional. Devido à capacidade do Stream Analytics para processar um grande número de eventos em tempo real, o aplicativo é altamente escalonável sem a necessidade de aumentar a camada de armazenamento de dados. A camada de armazenamento de dados é, essencialmente, uma exibição materializada no sistema. [Saída do Stream Analytics do Azure ao Azure Cosmos DB](stream-analytics-documentdb-output.md) descreve como o Cosmos DB é usado como uma saída do Stream Analytics.

Em aplicativos reais, em que a lógica de processamento é complexo e não houver é a necessidade de atualizar determinadas partes da lógica de forma independente, vários trabalhos do Stream Analytics podem ser compostos em conjunto com Hubs de eventos como o intermediário event broker.

![Aplicativo de fornecimento de eventos complexos do ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Esse padrão melhora a resiliência e a capacidade de gerenciamento do sistema. No entanto, mesmo se o Stream Analytics garante o processamento exatamente uma vez, há uma pequena chance de que eventos duplicados podem ir para os Hubs de eventos intermediário. É importante para o trabalho de Stream Analytics downstream para eventos usando chaves de lógica em uma janela lookback de eliminação de duplicação. Para obter mais informações sobre a entrega de eventos, consulte [garantias de entrega de evento](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) referência.

## <a name="use-reference-data-for-application-customization"></a>Usar dados de referência para personalização do aplicativo

O recurso de dados de referência do Azure Stream Analytics foi projetado especificamente para personalização do usuário final, como alertas de limite, regras de processamento, e [limites geográficos](geospatial-scenarios.md). A camada de aplicativo pode aceitar as alterações de parâmetro e armazená-las em um banco de dados SQL. O trabalho do Stream Analytics periodicamente consulta para que as alterações do banco de dados e faz com que os parâmetros de personalização acessível por meio de uma associação de dados de referência. Para obter mais informações sobre como usar dados de referência para personalização do aplicativo, consulte [dados de referência do SQL](sql-reference-data.md) e [associação de dados de referência](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Esse padrão também pode ser usado para implementar um mecanismo de regras em que os limites das regras são definidos de dados de referência. Para obter mais informações sobre regras, consulte [processar as regras baseadas em limite configuráveis no Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![Aplicativo de dados de referência do ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Adicionar o aprendizado de máquina para suas informações em tempo real

Interno do Azure Stream Analytics [modelo de detecção de anomalias](stream-analytics-machine-learning-anomaly-detection.md) é uma maneira conveniente para introduzir o aprendizado de máquina para seu aplicativo em tempo real. Para as necessidades de um intervalo mais amplo de aprendizado de máquina, consulte [do Azure Stream Analytics integra-se com o serviço de pontuação do Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Para usuários avançados que desejam incorporar o treinamento online e pontuação no mesmo pipeline do Stream Analytics, consulte este exemplo de como fazê-lo com [regressão linear](stream-analytics-high-frequency-trading.md).

![Aplicativo de aprendizado de máquina do ASA](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Quase em tempo real data warehouse

Outro padrão comum é em tempo real data warehouse, também chamado de depósito de dados de streaming. Além dos eventos que chegam ao IoT Hub e de Hubs de eventos de seu aplicativo, [em execução no IoT Edge do Azure Stream Analytics](stream-analytics-edge.md) pode ser usado para atender a limpeza de dados, redução de dados e armazenamento de dados e necessidades de encaminhamento. Análise de Stream em execução no IoT Edge normalmente pode lidar com problemas de conectividade e a limitação de largura de banda no sistema. O adaptador de saída do SQL pode ser usado para a saída para o SQL Data Warehouse; No entanto, a taxa de transferência máxima é limitada a 10 MB/s.

![ASA Data Warehousing](media/stream-analytics-solution-patterns/datawarehousing.png)

Uma maneira de melhorar a taxa de transferência com alguns compensação de latência é arquivar os eventos para o armazenamento de BLOBs do Azure e, em seguida [importá-los no SQL Data Warehouse com o Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Você deve manualmente compor a saída do Stream Analytics no armazenamento de BLOBs e entrada do armazenamento de blob ao SQL Data Warehouse por [arquivar os dados pelo carimbo de hora](stream-analytics-custom-path-patterns-blob-storage-output.md) e importando periodicamente.

Nesse padrão de uso, o Azure Stream Analytics é usado como um mecanismo ETL quase em tempo real. Recentemente eventos que chegam continuamente são transformados e armazenados para consumo do serviço de análise de downstream.

![ASA alta taxa de transferência de Data warehouse](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Arquivamento de dados em tempo real para análise

A maioria das atividades de ciência e análise de dados ainda ocorrem off-line. Dados podem ser arquivados pelo Azure Stream Analytics por meio do Azure Data Lake Store Gen2 saída e formatos de saída do Parquet. Esse recurso remove a fricção para alimentar os dados diretamente no Azure Data Lake Analytics, Azure Databricks e Azure HDInsight. O Azure Stream Analytics é usado como um mecanismo ETL quase em tempo real nesta solução. Você pode explorar os dados arquivados no Data Lake usando diversos mecanismos de computação.

![Análise offline de ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Usar dados de referência para aprimoramento

Enriquecimento de dados geralmente é um requisito para os mecanismos de ETL. O Azure Stream Analytics dá suporte ao enriquecimento de dados com [dados de referência](stream-analytics-use-reference-data.md) do banco de dados SQL e armazenamento de BLOBs do Azure. Enriquecimento de dados pode ser feito para dados de aterrissagem no Azure Data Lake e o SQL Data Warehouse.

![Análise ASA offline com o enriquecimento de dados](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Colocar em operação informações dos dados arquivados

Se você combinar o padrão de análise offline com o aplicativo em tempo real quase padrão, você pode criar um loop de comentários. O loop de comentários permite que o aplicativo ajustar automaticamente para alterar os padrões nos dados. Esse loop de comentários pode ser tão simples quanto alterar o valor de limite para o alerta, ou tão complexo quanto retreinamento de modelos de aprendizado de máquina. A mesma arquitetura da solução pode ser aplicada a ambos os trabalhos do ASA em execução na nuvem e no IoT Edge.

![Operacionalização de insights ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Como monitorar trabalhos do ASA

Um trabalho do Azure Stream Analytics pode ser executado 24x7 para processar eventos de entrada continuamente em tempo real. Sua garantia de funcionamento é crucial para a integridade do aplicativo geral. Embora o Stream Analytics é o único serviço de análise de streaming do setor que oferece uma [garantia de 99,9% de disponibilidade](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), você ainda incorrerá em algum nível de tempo de inatividade. Ao longo dos anos, o Stream Analytics introduziu as métricas, logs e estados de trabalho para refletir a integridade dos trabalhos. Todas elas são exibidas por meio do serviço do Azure Monitor e podem ser exportadas ainda mais ao OMS. Para obter mais informações, consulte [entender o Stream Analytics do trabalho de monitoramento e como monitorar consultas](stream-analytics-monitoring.md).

![Monitoramento de ASA](media/stream-analytics-solution-patterns/monitoring.png)

Há duas coisas principais para monitorar:

- [Estado de falha de trabalho](job-states.md)

    Primeiramente, você precisa certificar-se de que o trabalho está em execução. Sem o trabalho no estado de execução, não há novas métricas ou logs são gerados. Trabalhos podem mudar para um estado com falha por vários motivos, incluindo um alto nível de utilização de SU (ou seja, ficando sem recursos).

- [Métricas de atraso de marca d'água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Essa métrica reflete como que ficaram para trás o processamento de pipeline está na hora do relógio (segundos). Alguns do atraso é atribuído para a lógica de processamento inerente. Como resultado, a tendência de aumento de monitoramento é muito mais importante do que o valor absoluto de monitoramento. O atraso de estado estacionário deve ser resolvido pelo seu design de aplicativo, não pelo monitoramento ou alertas.

Em caso de falha, logs de atividades e [logs de diagnóstico](stream-analytics-job-diagnostic-logs.md) são os melhores locais para começar a procurar por erros.

## <a name="build-resilient-and-mission-critical-applications"></a>Compilação resiliente e aplicativos de missão crítica

Independentemente da garantia de SLA do Azure Stream Analytics e como cuidado você executar o aplicativo de ponta a ponta, interrupções acontecem. Se seu aplicativo for de missão crítica, você precisa estar preparado para interrupções para se recuperar normalmente.

Para aplicativos de alertas, a coisa mais importante é detectar o alerta ao lado. Você pode optar por reiniciar o trabalho do atual de tempo durante a recuperação, ignorando alertas passados. A semântica de tempo de início do trabalho é pela primeira vez de saída, não a primeira hora de entrada. A entrada é rebobinada com versões anteriores uma quantidade apropriada de tempo para garantir que a primeira saída na hora especificada está completo e correto. Você não obtém as agregações parciais e disparar alertas inesperadamente como resultado.

Você também pode optar por iniciar a saída de um determinado período de tempo no passado. As políticas de retenção de Hubs de eventos e um IoT Hub mantêm uma quantidade razoável de dados para permitir o processamento do passado. A desvantagem é a velocidade, você pode se inteirar com a hora atual e começar a gerar novos alertas em tempo hábil. Dados perdem seu valor rapidamente ao longo do tempo, portanto, é importante para se atualizar rapidamente para a hora atual. Há duas maneiras de se inteirar rapidamente:

- Provisione mais recursos (SU) quando a captura.
- Reinicie a partir da hora atual.

O tempo de reinicialização desde atual é simple de fazer, com a compensação de deixar uma lacuna durante o processamento. Reiniciar dessa maneira pode ser Okey para alertar os cenários, mas pode ser problemático para cenários de dashboard e é um não início de arquivamento e cenários de data warehouse.

Mais recursos de provisionamento pode acelerar o processo, mas o efeito de ter um aumento da taxa de processamento é complexo.

- Teste de que seu trabalho é dimensionável para um grande número de SUs. Nem todas as consultas são escalonáveis. Você precisará certificar-se de sua consulta está [paralelizado](stream-analytics-parallelization.md).

- Verifique se há partições suficientes no upstream dos Hubs de eventos ou IoT Hub que você pode adicionar mais taxa de transferência de TUs (unidades) para dimensionar a taxa de transferência de entrada. Lembre-se, cada TU de Hubs de eventos for maximizada com uma taxa de saída de 2 MB/s.

- Verifique se você tiver provisionado recursos suficientes em Coletores de saída (ou seja, o banco de dados SQL, o Cosmos DB), para que eles não limitar o aumento na saída, que pode, às vezes, fazer com que o sistema travar.

A coisa mais importante é prever a alteração da taxa de processamento, testar esses cenários antes de entrar em produção e estar pronto para dimensionar o processamento corretamente durante o tempo de recuperação de falha.

No cenário extreme que eventos de entrada são todas atrasada [é todos os possíveis eventos atrasados são descartados](stream-analytics-time-handling.md) se você tiver aplicado uma janela que chegam atrasada ao seu trabalho. A eliminação de eventos pode parecer um comportamento misterioso no início; No entanto, considerando que o Stream Analytics é um mecanismo de processamento em tempo real, ele espera de eventos de entrada para estar próximo a hora do relógio. Ele deve descartar eventos que violam essas restrições.

### <a name="backfilling-process"></a>O aterramento processo

Felizmente, o padrão de arquivamento de dados anterior pode ser usado para processar esses eventos tardios normalmente. A ideia é que o trabalho de arquivamento processa eventos de entrada na hora de chegada e arquivos mortos de eventos para o bucket de momento certo no Blob do Azure ou Azure Data Lake Store com seu tempo de evento. Não importa como tardia um evento chegar, ela nunca será descartada. Ele sempre serão levadas no bucket de hora certa. Durante a recuperação, é possível reprocessar os eventos arquivados e o aterramento os resultados para o repositório de escolha.

![Aterramento ASA](media/stream-analytics-solution-patterns/backfill.png)

O processo de compensação deve ser feito com um sistema, o que provavelmente tem um modelo de programação diferente do que o Azure Stream Analytics de processamento de lotes offline. Isso significa que você precisará reimplementar a lógica de processamento inteiro.

Para o aterramento, ele ainda é importante para pelo menos temporariamente provisionar que mais recursos para a saída de Coletores para lidar com a taxa de transferência maior que o estado estável necessidades de processamento.

|Cenários  |Reiniciar agora apenas  |Reiniciar da hora da última interrupção |Reiniciar a partir de agora + aterramento com eventos arquivados|
|---------|---------|---------|---------|
|**Dashboarding**   |Cria a lacuna    |Okey para breve interrupção    |Use para interrupção longa |
|**Alertas**   |Aceitável |Okey para breve interrupção    |Não é necessário |
|**Aplicativo de fornecimento do evento** |Aceitável |Okey para breve interrupção    |Use para interrupção longa |
|**Data warehouse**   |Perda de dados  |Aceitável |Não é necessário |
|**Análise offline**  |Perda de dados  |Aceitável |Não é necessário|

## <a name="putting-it-all-together"></a>Juntando as peças

Não é difícil imaginar que os padrões de solução mencionados acima podem ser combinados em um sistema complexo de ponta a ponta. O sistema combinado pode incluir painéis, alertas, aplicativo de fornecimento de eventos, de data warehouse e recursos de análise offline.

A chave é projetar seu sistema em padrões combináveis, para que cada subsistema pode ser criado, testado, atualizado, e se recuperar de forma independente.

## <a name="next-steps"></a>Próximas etapas

Agora você já viu uma variedade de padrões de solução usando o Azure Stream Analytics. Em seguida, você pode se aprofundar e criar seu primeiro trabalho do Stream Analytics:

* [Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md).
* [Criar um trabalho do Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Criar um trabalho do Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).
