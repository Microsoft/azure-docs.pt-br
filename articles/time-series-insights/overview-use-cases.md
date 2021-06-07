---
title: Casos de uso Gen2-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre Azure Time Series Insights casos de uso do Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606956"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Casos de uso do Azure Time Series Insights Gen2

Este artigo resume vários casos de uso comuns para Azure Time Series Insights Gen2. As recomendações neste artigo servem como um ponto de partida para desenvolver seus aplicativos e soluções com Azure Time Series Insights Gen2.

Especificamente, este artigo responde às seguintes perguntas:

* Quais são os casos de uso comuns para Azure Time Series Insights Gen2?
* Quais são os benefícios de usar Azure Time Series Insights Gen2 para [exploração de dados e detecção de anomalias visuais](#data-exploration-and-visual-anomaly-detection)?
* Quais são os benefícios de usar o Azure Time Series Insights Gen2 para a [análise operacional e a eficiência do processo](#operational-analysis-and-driving-process-efficiency)?
* Quais são os benefícios de usar o Azure Time Series Insights Gen2 para [análise avançada](#advanced-analytics)?

Uma visão geral desses cenários de uso é descrita nas seções a seguir.

## <a name="introduction"></a>Introdução

Azure Time Series Insights Gen2 é uma oferta de plataforma como serviço de ponta a ponta. É usado para coletar, processar, armazenar, analisar e consultar dados de escala de IoT otimizados para série temporal e altamente contextualizados. É ideal para exploração de dados ad hoc e análise operacional. Azure Time Series Insights Gen2 é uma oferta de serviço personalizada e extensível de forma exclusiva que atende às amplas necessidades de implantações de IoT industriais.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Exploração de dados e detecção visual de anomalias

Explore e analise instantaneamente bilhões de eventos para identificar anomalias e descobrir tendências ocultas nos seus dados. Azure Time Series Insights Gen2 oferece desempenho quase em tempo real para suas cargas de trabalho de análise de IoT e DevOps.

[![Data Explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A maioria dos clientes concorda que a quantidade mínima de tempo necessária para obter informações é um dos recursos principal de Azure Time Series Insights Gen2:

* Azure Time Series Insights Gen2 não requer preparação de dados antecipada.
* Ele funciona rapidamente para se conectar a bilhões de eventos em suas instâncias do Hub IoT do Azure ou dos hubs de eventos do Azure em minutos.
* Uma vez conectado, você pode visualizar e analisar bilhões de eventos para detecção de anomalias e descobrir tendências ocultas em seus dados.

Azure Time Series Insights Gen2 é intuitivo e simples de usar. Você pode interagir com seus dados sem escrever uma única linha de código. Também não há uma nova linguagem que você precisa aprender, embora Azure Time Series Insights Gen2 forneça uma linguagem de consulta granular baseada em texto para usuários avançados que estão familiarizados com o SQL. Também fornece exploração de seleção e clique para iniciantes.

Os clientes podem aproveitar a velocidade para diagnosticar problemas relacionados ao ativo rapidamente. Eles podem executar a análise de DevOps para chegar à causa raiz de um bug em uma solução de IoT. Eles também podem identificar áreas a serem sinalizadas para uma investigação mais aprofundada como parte de suas iniciativas de ciência de dados.

Há três maneiras principais de interagir com os dados armazenados no Azure Time Series Insights Gen2:

* A primeira e mais simples maneira de começar é com o Azure Time Series Insights Gen2 Explorer. Você pode usá-lo para visualizar rapidamente todos os dados de IoT em um só lugar. Ele fornece ferramentas como o mapa de calor para ajudá-lo a identificar anomalias em seus dados. Ele também fornece uma exibição perspectiva. Use-o para comparar até quatro modos de exibição de um ou mais ambientes de Azure Time Series Insights Gen2 em um único painel. O painel fornece uma exibição de seus dados de série temporal em todos os seus locais. Saiba mais sobre o [Azure Time Series insights Explorer Gen2](./concepts-ux-panels.md). Para planejar seu ambiente, leia [Azure Time Series insights planejamento do Gen2](./how-to-plan-your-environment.md).

* A segunda maneira de começar é usar o SDK do JavaScript para inserir rapidamente gráficos e grafos poderosos em seu aplicativo Web. Com apenas algumas linhas de código, você pode criar consultas avançadas. Use-os para preencher gráficos de linhas, gráficos de pizza, gráficos de barras, mapas de calor, grades de dados e muito mais. Todos esses elementos existem prontos para uso com o SDK. O SDK também abstrai Azure Time Series Insights APIs de consulta Gen2. Você pode usá-las para criar predicados do tipo SQL para consultar os dados que você deseja mostrar em um painel. Para soluções de camada de apresentação híbrida, Azure Time Series Insights Gen2 oferece URLs com parâmetros. Eles fornecem pontos de conexão contínuos com o Azure Time Series Insights Explorer Gen2 para aprofundar-se nos dados.

  * Leia sobre a [biblioteca de cliente js](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e a documentação do [cliente de exemplo](https://github.com/Microsoft/tsiclient) para saber mais sobre o SDK do JavaScript.

  * Saiba mais sobre como compartilhar URLs e a nova interface do usuário examinando [Visualizar dados no Azure Time Series insights Explorer Gen2](./concepts-ux-panels.md).

* A terceira maneira de começar é usar as APIs poderosas para consultar dados armazenados no Azure Time Series Insights Gen2. Azure Time Series insights Gen2 tem operadores temporais, como `from` , `to` , `first` e `last` . Ele tem agregações e transformações, como,,,, `average` `sum` `min` `max` `time-weighted average` , `time-weighted sum` etc. Ele também permite filtragem, operadores aritméticos e boolianos, funções escalares, etc. Todos esses operadores permitem que aplicativos downstream encontrem rapidamente padrões e tendências interessantes em seus dados. Use-os para preencher visualizações domésticas para identificar anomalias.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Análise operacional e como impulsionar a eficiência do processo

Use Azure Time Series Insights Gen2 para monitorar a integridade, o uso e o desempenho dos equipamentos em escala e medir a eficiência operacional. Azure Time Series Insights Gen2 ajuda a gerenciar cargas de trabalho de IoT diversificadas e imprevisíveis sem sacrificar o desempenho de ingestão ou consulta.

[![A captura de tela mostra os dispositivos e os dados de aplicativos, o processamento de fluxo, a eficiência operacional, a inteligência/informações e a análise avançada no Azure Time Series Insights Gen2.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streaming e processamento contínuo de dados provenientes de processos operacionais podem transformar com sucesso qualquer negócio quando combinados com a tecnologia ou a solução certa. Geralmente, essas soluções são uma combinação de vários sistemas. Eles permitem a exploração e a análise de dados que são alterados constantemente, especialmente no realm de IoT, e compartilham um padrão comum.

Esses padrões costumam começar com plataformas habilitadas para IoT que ingerem bilhões de eventos de dispositivos e sensores que abrangem várias localidades. Esses sistemas processam e analisam dados de streaming para obterem ações e insights em tempo real. Normalmente, os dados são arquivados na loja passiva e fria para análise de lote quase em tempo real.

Os dados coletados passam por uma série de processamento para limpeza e contextualização para cenários de análise e consulta downstream. O Azure oferece serviços avançados que podem ser aplicados a cenários de IoT, como fabricação e manutenção de ativo. Esses serviços incluem Azure Time Series Insights Gen2, Hub IoT, hubs de eventos, Azure Stream Analytics, Azure Functions, aplicativos lógicos do Azure, Azure Databricks, Azure Machine Learning e Power BI.

A arquitetura da solução pode ser obtida da seguinte maneira:

* Ingestão de dados por meio do Hub IoT ou Hubs de Eventos para a melhor segurança, taxa de transferência e latência da categoria.
* Execute o processamento de dados e computação. Os dados ingeridos por funil por meio de serviços, como o Stream Analytics, Aplicativos Lógicos e Azure Functions. O serviço que você usa depende das necessidades específicas de processamento de dados.
* Os sinais computados do pipeline de processamento são enviados por push para Azure Time Series Insights Gen2 para armazenamento e análise.

Azure Time Series Insights Gen2 oferece exploração de dados quase em tempo real e informações baseadas em ativos sobre dados históricos. Dependendo das suas necessidades de negócios, os trabalhos do MapReduce e do hive podem ser executados em dados armazenados no Azure Time Series Insights Gen2, conectando Azure Time Series Insights Gen2 ao Azure HDInsight. Os dados armazenados no Azure Time Series Insights Gen2 estão disponíveis para Power BI e outros aplicativos de clientes por meio das APIs de consulta de superfície pública Azure Time Series Insights Gen2. Esses dados podem ser usados para amplos cenários de inteligência operacional e empresarial.

## <a name="advanced-analytics"></a>Análise avançada

Integre com serviços de análise avançados como o Machine Learning e o Azure Databricks. Azure Time Series Insights Gen2 insere dados brutos de milhões de dispositivos. Ele adiciona dados contextuais que podem ser consumidos diretamente por um pacote de serviços de análise do Azure.

[![Análise](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

O aprendizado de máquina e a análise avançada consomem e processam grandes volumes de dados. Esses dados são usados para tomar decisões controladas por dados e executar a análise preditiva. Em casos de uso de IoT, algoritmos de análise avançada aprendem com os dados coletados de milhões de dispositivos. Esses dispositivos transmitem dados várias vezes por segundo. Os dados coletados dos dispositivos de IoT são brutos. Eles não têm informações contextuais, como a localização do dispositivo e a unidade da leitura de sensor. Como resultado, os dados brutos são difíceis de consumir diretamente para análise avançada.

Azure Time Series Insights Gen2 preenche a lacuna entre os dados de IoT e a análise avançada de duas maneiras simples e econômicas:

* Primeiro, Azure Time Series Insights Gen2 coleta dados de telemetria brutos de milhões de dispositivos usando o Hub IoT. Ele enriquece dados com informações contextuais e transforma os dados em um formato parquet. Esse formato pode facilmente se integrar a outros serviços de análise avançada, como Machine Learning, Azure Databricks e aplicativos de terceiros.

    Azure Time Series Insights Gen2 pode servir como a fonte de verdade para todos os dados em toda a organização. Ele cria um repositório central para cargas de trabalho de análise downstream consumirem. Como Azure Time Series Insights Gen2 é um serviço de armazenamento quase em tempo real, os modelos de análise avançada podem aprender continuamente com dados de telemetria de IoT recebidos. Como resultado, os modelos podem fazer previsões mais precisas.

* Em segundo lugar, a saída dos modelos de aprendizado de máquina e de previsão pode ser inserida em Azure Time Series Insights Gen2 para visualizar e armazenar seus resultados. Esse procedimento ajuda as organizações a otimizar e ajustar seus modelos. Azure Time Series Insights Gen2 simplifica a visualização de dados de telemetria de streaming no mesmo plano que as saídas de modelo treinadas. Dessa forma, ele ajuda as equipes de ciência de dados a detectar anomalias e identificar padrões.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Time Series insights Explorer Gen2](./concepts-ux-panels.md).
* Leia [Azure Time Series insights práticas recomendadas do Gen2](./how-to-plan-your-environment.md) para planejar seu ambiente.
* Leia a documentação do [cliente de exemplo](https://github.com/Microsoft/tsiclient) .
