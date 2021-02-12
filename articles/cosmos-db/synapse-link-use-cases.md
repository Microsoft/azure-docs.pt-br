---
title: Casos de uso de análise em tempo real com o Link do Azure Synapse para Azure Cosmos DB
description: Saiba como o Link do Azure Synapse para Azure Cosmos DB é usado na análise da cadeia de fornecedores, na previsão, no relatório, na personalização em tempo real e na manutenção preditiva de IoT (Internet das Coisas).
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: 9855a53ebdf7501907b3e1e63d59823d3a0b0209
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463115"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Link do Azure Synapse para Azure Cosmos DB: casos de uso de análise quase em tempo real
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

O [Link do Azure Synapse](synapse-link.md) para Azure Cosmos DB é uma funcionalidade de HTAP (processamento transacional híbrido e analítico) nativa de nuvem que permite executar análises quase em tempo real sobre dados operacionais. O Link do Synapse cria uma integração perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics.

Você pode estar curioso para entender quais casos de uso do setor podem usar essa funcionalidade de HTAP nativa de nuvem para a análise quase em tempo real de dados operacionais. Estes são três casos de uso comuns do Link do Azure Synapse para Azure Cosmos DB:

* Análise de cadeia de fornecedores, previsão e relatórios
* Personalização em tempo real
* Manutenção preditiva, detecção de anomalias em cenários de IoT

> [!NOTE]
> O Link do Azure Synapse para Azure Cosmos DB se destina ao cenário em que as equipes empresariais estão procurando executar análises quase em tempo real. Essas análises são executadas sem ETL (extração, transformação e carregamento) de dados operacionais gerados em aplicativos transacionais criados no Azure Cosmos DB. Isso não substitui a necessidade de um data warehouse separado quando houver requisitos de data warehouse tradicional, como gerenciamento de carga de trabalho, alta simultaneidade, persistência com agregação entre várias fontes de dados.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Análise de cadeia de fornecedores, previsão e relatórios

Pesquisas mostram que a inserção de análises de Big Data em operações de cadeia de suprimentos leva a melhorias nos tempos de entrega desde o pedido até o ciclo e na eficiência da cadeia de fornecedores.

Os fabricantes estão passando a usar a tecnologias nativas de nuvem para acabar com as restrições dos sistemas de ERP (planejamento de recursos empresariais) e de SCM (gerenciamento de cadeia de fornecedores) herdados. Com cadeias de fornecimento gerando volumes cada vez maiores de dados operacionais a cada minuto (pedido, envio, dados de transação), os fabricantes precisam de um banco de dado operacional. Esse banco de dados operacional deve ser dimensionado para lidar com os volumes de dados, bem como uma plataforma analítica para chegar a um nível de inteligência contextual em tempo real para ficar à frente da curva.

A arquitetura a seguir mostra o poder de usar o Azure Cosmos DB como o banco de dados operacional nativo de nuvem e o Link do Synapse na análise da cadeia de fornecedores:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Link do Azure Synapse para Azure Cosmos DB na análise de cadeia de suprimentos " border="false":::

Com base na arquitetura anterior, você pode obter os seguintes casos de uso com o Link do Synapse para Azure Cosmos DB:

* **Preparar e treinar o pipeline preditivo:** gere insights sobre os dados operacionais na cadeia de suprimentos usando o aprendizado de máquina. Dessa forma, você pode diminuir o inventário, os custos operacionais e reduzir o tempo entre o pedido e a entrega para os clientes.

  O Link do Synapse permite que você analise a alteração de dados operacionais no Azure Cosmos DB sem nenhum processo de ETL manual. Ele evita custos adicionais, a latência e a complexidade operacional. O Link do Synapse permite que engenheiros de dados e cientistas de dados criem pipelines preditivos robustos:

  * Consulte dados operacionais do repositório analítico do Azure Cosmos DB usando a integração nativa com pools do Apache Spark no Azure Synapse Analytics. Você pode consultar os dados em um notebook interativo ou em trabalhos remotos agendados sem uma engenharia de dados complexa.

  * Crie modelos de ML (Machine Learning) com algoritmos de ML do Spark e a integração do Azure ML no Azure Synapse Analytics.

  * Faça write-back dos resultados após a inferência de modelo no Azure Cosmos DB para a pontuação operacional quase em tempo real.

* **Relatórios operacionais:** as equipes da cadeia de fornecedores precisam de relatórios flexíveis e personalizados em dados operacionais precisos e em tempo real. Esses relatórios são necessários para a obtenção de uma exibição de instantâneo da eficácia, lucratividade e produtividade da cadeia de fornecedores. Isso permite que analistas de dados e outros stakeholders importantes reavaliem constantemente os negócios e identifiquem as áreas a serem ajustadas para reduzir os custos operacionais. 

  O Link do Synapse para Azure Cosmos DB habilita a criação de cenários ricos de BI (business intelligence)/relatórios:

  * Consultar dados operacionais de Azure Cosmos DB repositório analítico usando a integração nativa com o pool SQL sem servidor e a expressividade completa da linguagem T-SQL.

  * Modele e publique os painéis de BI de atualização automática em Azure Cosmos DB por meio do suporte a pool do SQL sem servidor para ferramentas de BI familiares. Por exemplo, Azure Analysis Services, Power BI Premium etc.

A seguir, há algumas diretrizes para a integração de dados do lote e de dados de streaming ao Azure Cosmos DB:

* **Integração e orquestração de dados em lotes:** com as cadeias de fornecimento ficando mais complexas, as plataformas de dados da cadeia de fornecedores precisam ser integradas com uma variedade de fontes de dados e formatos. O Azure Synapse vem integrado com o mesmo mecanismo de integração de dados e experiências que o Azure Data Factory. Essa integração permite que engenheiros de dados criem pipelines de dados avançados sem um mecanismo de orquestração separado:

  * Mova dados de mais de 85 fontes de dados com suporte para o [Azure Cosmos DB com Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Escreva pipelines de ETL sem código para o Azure Cosmos DB incluindo [mapeamentos relacionais a hierárquicos e hierárquicos a hierárquicos com fluxos de dados de mapeamento](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Streaming de integração de dados e processamento:** Com o crescimento da IoT industrial (ativos de acompanhamento de sensores da “produção para comercialização”, as frotas de logística conectadas etc.), há uma explosão de dados em tempo real que são gerados em forma de streaming que precisa ser integrada a dados de movimentação lenta tradicionais para gerar insights. O Azure Stream Analytics é um serviço recomendado para o streaming de ETL e o processamento no Azure com uma [ampla variedade de cenários](../stream-analytics/streaming-technologies.md). O Azure Stream Analytics oferece suporte ao [Azure Cosmos DB como um de coletor de dados nativo](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Personalização em tempo real

Atualmente, os varejistas precisam criar soluções de comércio eletrônico seguras e escalonáveis que atendam às demandas empresariais e dos clientes. Essas soluções de comércio eletrônico precisam envolver os clientes por meio de ofertas e produtos personalizados, processe transações de maneira rápida e segura e concentre-se na entrega e no atendimento ao cliente. O Azure Cosmos DB, junto com o Link do Synapse mais recente para Azure Cosmos DB, permite que os varejistas gerem recomendações personalizadas para clientes em tempo real. Eles usam configurações de consistência ajustáveis e de baixa latência para insights imediatas, conforme mostrado na seguinte arquitetura:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Link do Azure Synapse para Azure Cosmos DB com personalização em tempo real" border="false":::

Caso de uso do Link do Synapse para Azure Cosmos DB:

* **Preparar e treinar o pipeline preditivo:** Você pode gerar insights sobre os dados operacionais em suas unidades de negócios ou segmentos de clientes usando os modelos de machine learning e Synapse Spark. Isso se traduz na entrega personalizada que se destina a segmentos de clientes, experiências de usuário final preditivas e marketing direcionado para atender aos seus requisitos de usuário final.

## <a name="iot-predictive-maintenance"></a>Manutenção preditiva da IoT

As inovações na IoT industrial reduziram drasticamente o tempo de inatividade de máquinas e aumentaram a eficiência geral em todos os campos da indústria. Uma dessas inovações é a análise de manutenção preditiva de máquinas na borda da nuvem.

A seguir, há uma arquitetura que usa as funcionalidades de HTAP nativos de nuvem do Link do Azure Synapse para Azure Cosmos DB na manutenção preditiva da IoT:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="O Link do Azure Synapse para Azure Cosmos DB na manutenção preditiva da IoT" border="false" :::

Casos de uso do Link do Synapse para Azure Cosmos DB:

* **Preparar e treinar o pipeline preditivo:** Os dados operacionais históricos dos sensores do dispositivo de IoT poderiam ser usados para treinar modelos preditivos, como detectores de anomalias. Esses detectores de anomalias seriam então reimplantados na borda para a realização do monitoramento em tempo real. Esse loop virtuoso permite o retreinamento contínuo dos modelos preditivos.

* **Relatórios operacionais:** Com o crescimento de iniciativas de gêmeos digitais, as empresas estão coletando grandes quantidades de dados operacionais de um grande número de sensores para criar uma cópia digital de cada computador. Esses dados aumentam a necessidade de a BI compreender as tendências em relação aos dados históricos, além dos aplicativos em tempo real em relação aos dados de acesso frequente.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Cenário de exemplo: HTAP do Azure Cosmos DB

Por quase uma década, Azure Cosmos DB foi usada por milhares de clientes para aplicativos de missão crítica que exigem escala elástica, distribuição global completa, replicação de gravação de várias regiões para baixa latência e alta disponibilidade de leituras & gravações em suas cargas de trabalho transacionais.
 
A lista a seguir mostra uma visão geral dos diversos padrões de carga de trabalho suportados com dados operacionais que usam o Azure Cosmos DB:

* Aplicativos e serviços em tempo real
* Processamento de streaming de eventos
* Painéis de BI
* Análise de Big Data
* Aprendizado de máquina

O Link do Azure Synapse permite que o Azure Cosmos DB não apenas capacite cargas de trabalho transacionais, como também execute cargas de trabalho analíticas quase em tempo real em relação a dados operacionais históricos. Isso acontece sem requisitos de ETL e com a garantia de isolamento de desempenho das cargas de trabalho transacionais.

A imagem a seguir mostra os padrões de carga de trabalho usando o Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Link do Azure Synapse para padrões de carga de trabalho do Azure Cosmos DB" border="false":::

Vamos pegar o exemplo de uma empresa de comércio eletrônico, a EmpresaXYZ, com operações globais em 20 países/regiões para ilustrar os benefícios por escolher o Azure Cosmos DB como o único banco de dados em tempo real, o que capacita os requisitos transacionais e analíticos de uma plataforma de gerenciamento de inventário.

* O principal negócio da EmpresaXYZ depende do sistema de gerenciamento de inventário, portanto, a disponibilidade e confiabilidade são os requisitos principais do pilar. Benefícios do uso do Azure Cosmos DB:

  * Em virtude de uma integração profunda com a infraestrutura do Azure e gravações de várias regiões transparentes, a replicação global Azure Cosmos DB fornece a [alta disponibilidade de 99,999%](high-availability.md) líder do setor contra interrupções regionais.

* Os parceiros da cadeia de fornecedores da EmpresaXYZ podem estar em locais geográficos separados, mas talvez precisem ver uma única exibição do inventário de produtos de todo o mundo para corroborar suas operações locais. Isso inclui a necessidade de ter acesso em tempo real às atualizações feitas por outros parceiros da cadeia de fornecedores. Além disso, também é preciso haver a possibilidade de fazer atualizações sem se preocupar com entradas conflitantes com outros parceiros sob alta taxa de transferência. Benefícios do uso do Azure Cosmos DB:

  * Com seu único protocolo de replicação de gravações de várias regiões e armazenamento transacional com otimização de gravação e sem trava, Azure Cosmos DB garante a redução de latências de menos de 10 ms para leituras indexadas e gravações no 99 º percentil globalmente.

  * A ingestão de alta taxa de transferência de feeds de dados de streaming e lote com [indexação em tempo real](index-policy.md) no repositório transacional.

  * O repositório transacional do Azure Cosmos DB fornece três opções a mais do que os dois extremos de níveis de consistência forte e eventual para alcançar as [compensações entre disponibilidade e desempenho](./consistency-levels.md) mais próximas à necessidade de negócios.

* Os parceiros da cadeia de fornecedores da EmpresaXYZ têm padrões de tráfego altamente flutuantes de centenas a milhões de solicitações por segundo e, portanto, a plataforma de gerenciamento de estoque precisa lidar com intermitências inesperadas de tráfego.  Benefícios do uso do Azure Cosmos DB:

  * O repositório transacional do Azure Cosmos DB tem suporte para a escalabilidade elástica de armazenamento e taxa de transferência usando o particionamento horizontal. Os contêineres e bancos de dados configurados no Autopilot podem escalar de forma automática e instantânea a taxa de transferência provisionada com base nas necessidades do aplicativo sem impactar a disponibilidade, a latência, a taxa de transferência ou o desempenho da carga de trabalho globalmente.

* A EmpresaXYZ precisa estabelecer uma plataforma de análise segura para acomodar dados de inventário históricos de todo o sistema para habilitar análises e insights entre parceiros de cadeia de fornecedores, unidades de negócios e funções. A plataforma de análise precisa habilitar a colaboração em todo o sistema, casos de uso tradicionais de BI/relatórios, casos de uso de análise avançada e soluções inteligentes preditivas quanto aos dados de inventário operacionais. Benefícios por usar o Link do Synapse para Azure Cosmos DB:

  * Ao usar o [repositório analítico do Azure Cosmos DB](analytical-store-introduction.md), um repositório de coluna totalmente isolado, o Link do Synapse não permite nenhuma análise de ETL no [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) contra dados operacionais distribuídos globalmente em escala.  Agora analistas de negócios, engenheiros de dados e cientistas de dados podem usar o Synapse Spark ou o SQL do Synapse de maneira interoperável para executar pipelines de business intelligence, análise e aprendizado de máquina quase em tempo real sem impactar o desempenho de suas cargas de trabalho transacionais no Azure Cosmos DB. Consulte os [benefícios do Link do Synapse no Azure Cosmos DB](synapse-link.md) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte a seguinte documentação:

* [Link do Azure Synapse para Azure Cosmos DB](synapse-link.md) 

* [Repositório analítico do Azure Cosmos DB](analytical-store-introduction.md)

* [Como trabalhar com o Link do Azure Synapse para Azure Cosmos DB](configure-synapse-link.md)

* [Perguntas frequentes sobre o Link do Azure Synapse para Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Apache Spark no Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Suporte a tempo de execução do pool SQL sem servidor no Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)