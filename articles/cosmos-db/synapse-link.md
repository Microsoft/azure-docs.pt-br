---
title: Benefícios do Link do Azure Synapse para Azure Cosmos DB e quando usá-lo
description: Saiba mais sobre o Link do Azure Synapse para Azure Cosmos DB. O Link do Synapse permite executar análises quase em tempo real (HTAP) usando o Azure Synapse Analytics em dados operacionais no Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.openlocfilehash: ed909cf3feb17930b045dee1031ed5a6209b63d2
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029008"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>O que é o Link do Azure Synapse para Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> O suporte do pool SQL sem servidor Synapse para o link Synapse do Azure para Azure Cosmos DB está atualmente em versão prévia. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte os [Termos de uso complementares de versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Link do Azure Synapse para Azure Cosmos DB é uma funcionalidade de HTAP (processamento transacional híbrido e analítico) nativa de nuvem que permite executar análises quase em tempo real sobre dados operacionais. O Link do Azure Synapse cria uma integração perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics.

Ao usar o [repositório analítico do Azure Cosmos DB](analytical-store-introduction.md), um repositório de coluna totalmente isolado, o Link do Azure Synapse não permite nenhuma análise de ETL no [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) contra seus dados operacionais em escala. Agora analistas de negócios, engenheiros de dados e cientistas de dados podem usar o Synapse Spark ou o SQL do Synapse de forma intercambiável para executar pipelines de business intelligence, análise e aprendizado de máquina quase em tempo real. Isso pode ser alcançado sem afetar o desempenho de suas cargas de trabalho transacionais no Azure Cosmos DB. 

A imagem a seguir mostra a integração do Link do Azure Synapse com o Azure Cosmos DB e o Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Diagrama de arquitetura par integração do Azure Synapse Analytics com o Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Benefícios

Para analisar grandes conjuntos de dados operacionais e minimizar o impacto no desempenho de cargas de trabalho transacionais de missão crítica ao mesmo tempo, tradicionalmente, os dados operacionais no Azure Cosmos DB são extraídos e processados por pipelines de ETL (extração, transformação e carregamento). Os pipelines de ETL exigem muitas camadas de movimentação de dados, o que resulta em grande complexidade operacional e no impacto sobre o desempenho em suas cargas de trabalho transacionais. Eles também aumentam a latência para analisar os dados operacionais do tempo de origem.

Em comparação com as soluções tradicionais baseadas em ETL, o Link do Azure Synapse para Azure Cosmos DB oferece várias vantagens, como:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Complexidade reduzida sem trabalhos de ETL para gerenciar

O Link do Azure Synapse permite que você acesse diretamente o repositório analítico do Azure Cosmos DB usando o Azure Synapse Analytics sem movimentação de dados complexa. Quaisquer atualizações feitas nos dados operacionais são visíveis no repositório analítico quase em tempo real sem ETL ou trabalhos de feed de alterações. Você pode executar análises em larga escala no repositório analítico, do Azure Synapse Analytics, sem transformação de dados adicional.

### <a name="near-real-time-insights-into-your-operational-data"></a>Insights quase em tempo real sobre seus dados operacionais

Agora você pode obter insights detalhados sobre seus dados operacionais quase em tempo real, usando o Link do Azure Synapse. Os sistemas baseados em ETL tendem a ter maior latência para analisar seus dados operacionais, o que ocorre devido às muitas camadas necessárias para extrair, transformar e carregar os dados operacionais. Com a integração nativa do repositório analítico de Azure Cosmos DB com o Azure Synapse Analytics, você pode analisar dados operacionais em tempo quase real ao habilitar novos cenários de negócios. 

### <a name="no-impact-on-operational-workloads"></a>Sem impacto nas cargas de trabalho operacionais

Com o Link do Azure Synapse, você pode executar consultas analíticas em um repositório analítico de Azure Cosmos DB (um repositório de coluna separado) enquanto as operações transacionais são processadas usando a taxa de transferência provisionada para a carga de trabalho transacional (um repositório transacional baseado em linha).  A carga de trabalho analítica é fornecida independentemente do tráfego da carga de trabalho transacional e sem consumir nenhuma taxa de transferência provisionada para seus dados operacionais.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Otimizado para cargas de trabalho de análise em larga escala

O repositório analítico do Azure Cosmos DB é otimizado para fornecer escalabilidade, elasticidade e desempenho para cargas de trabalho analíticas sem qualquer dependência dos tempos de execução de computação. A tecnologia de armazenamento é autogerenciada para otimizar suas cargas de trabalho de análise. Com o suporte interno no Azure Synapse Analytics, o acesso a essa camada de armazenamento fornece simplicidade e alto desempenho.

### <a name="cost-effective"></a>Custo efetivo

Com o Link do Azure Synapse, você pode obter uma solução com otimização de custos e totalmente gerenciada para análise operacional. Ele elimina as camadas extras de armazenamento e computação necessárias em pipelines de ETL tradicionais para analisar os dados operacionais. 

O repositório analítico do Azure Cosmos DB segue um modelo de preços baseado em consumo, o qual se baseia no armazenamento de dados e nas operações e consultas de leitura/gravação analíticas executadas. Ele não requer que você provisione nenhuma taxa de transferência, que é o que você faz hoje para as cargas de trabalho transacionais. Acessar seus dados com mecanismos de computação altamente elásticos do Azure Synapse Analytics torna muito eficiente o custo geral da execução do armazenamento e da computação.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Análise de gravações de várias regiões disponíveis localmente, distribuídas globalmente

Você pode executar consultas analíticas efetivamente na cópia regional mais próxima dos dados no Azure Cosmos DB. O Azure Cosmos DB fornece a funcionalidade de última geração para executar as cargas de trabalho analíticas distribuídas globalmente juntamente com cargas de trabalho transacionais de maneira ativa-ativa.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Habilitar cenários de HTAP para seus dados operacionais

O Link do Synapse reúne o repositório analítico do Azure Cosmos DB com o suporte de runtime do Azure Synapse Analytics. Essa integração permite que você crie soluções de HTAP nativas de nuvem que geram insights com base em atualizações em tempo real para seus dados operacionais em grandes conjuntos de dados. Ela desbloqueia novos cenários de negócios para gerar alertas com base em tendências dinâmicas, criar painéis quase em tempo real e experiências de negócios com base no comportamento do usuário.

### <a name="azure-cosmos-db-analytical-store"></a>Repositório analítico do Azure Cosmos DB

O repositório analítico do Azure Cosmos DB é uma representação orientada por coluna de seus dados operacionais no Azure Cosmos DB. Esse repositório analítico é adequado para consultas rápidas e econômicas em grandes conjuntos de dados operacionais, sem copiar dados e impactar o desempenho de suas cargas de trabalho transacionais.

O repositório analítico seleciona automaticamente inserções, atualizações, exclusões de alta frequência em suas cargas de trabalho transacionais quase em tempo real, como um recurso totalmente gerenciado (“sincronização automática”) do Azure Cosmos DB. Não é necessário nenhum feed de alterações ou ETL. 

Se você tiver uma conta do Azure Cosmos DB distribuída globalmente, depois de habilitar o repositório analítico para um contêiner, ele ficará disponível em todas as regiões dessa conta. Para obter mais informações sobre o repositório analítico, consulte o artigo [Visão geral do repositório analítico do Azure Cosmos DB](analytical-store-introduction.md).

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integração com o Azure Synapse Analytics

Com o Link do Synapse, agora você pode se conectar diretamente aos seus contêineres do Azure Cosmos DB do Azure Synapse Analytics e acessar o repositório analítico sem conectores separados. A análise de Synapse do Azure atualmente dá suporte ao link Synapse com [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) e [pool de SQL sem servidor](../synapse-analytics/sql/on-demand-workspace-overview.md).

Você pode consultar os dados do repositório analítico do Azure Cosmos DB simultaneamente com a interoperabilidade entre diferentes tempos de execução de análise suportados pelo Azure Synapse Analytics. Não é necessária nenhuma transformação de dados adicional para analisar os dados operacionais. Você pode consultar e analisar os dados do repositório analítico usando:

* Synapse Apache Spark com suporte completo para Scala, Python, SparkSQL e C#. O Synapse Spark é fundamental para cenários de engenharia de dados e ciência de dados

* Pool SQL sem servidor com linguagem T-SQL e suporte para ferramentas de BI familiares (por exemplo, Power BI Premium, etc.)

> [!NOTE]
> No Azure Synapse Analytics, você pode acessar repositórios analíticos e transacionais em seu contêiner do Azure Cosmos DB. No entanto, se você quiser executar análises ou exames em larga escala em seus dados operacionais, recomendamos o uso do repositório analítico para evitar o impacto no desempenho em cargas de trabalho transacionais.

> [!NOTE]
> Você pode executar a análise com baixa latência em uma região do Azure conectando seu contêiner do Azure Cosmos DB ao runtime do Synapse nessa região.

Essa integração habilita os seguintes cenários de HTAP para diferentes usuários:

* Um engenheiro de BI que deseja modelar e publicar um relatório de Power BI para acessar os dados operacionais ao vivo em Azure Cosmos DB diretamente por meio do Synapse SQL.

* Um analista de dados que deseja obter insights a partir dados operacionais em um contêiner do Azure Cosmos DB por meio de consultas com o SQL do Synapse, da leitura dos dados em escala e da combinação dessas descobertas com outras fontes de dados.

* Um cientista de dados que deseja usar o Synapse Spark para encontrar um recurso para melhorar seu modelo e treinar esse modelo sem fazer uma engenharia de dados complexa. Ele também pode gravar os resultados da inferência de postagem do modelo no Azure Cosmos DB para pontuação em tempo real sobre os dados por meio do Spark Synapse.

* Um engenheiro de dados que deseja tornar os dados acessíveis para os consumidores por meio da criação de tabelas do SQL ou do Spark em contêineres do Azure Cosmos DB sem processos de ETL manuais.

Para obter mais informações sobre o suporte ao runtime do Azure Synapse Analytics para Azure Cosmos DB, consulte [Suporte do Azure Synapse Analytics para Cosmos DB](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Quando usar o Link do Azure Synapse para Azure Cosmos DB?

O Link do Synapse é recomendado nos seguintes casos:

* Se você for um cliente do Azure Cosmos DB e quiser executar análises, BI e aprendizado de máquina sobre seus dados operacionais. Nesses casos, o Link do Synapse fornece uma experiência de análise mais integrada sem afetar a taxa de transferência provisionada do repositório transacional. Por exemplo:

  * Se você estiver executando a análise ou a BI em seus dados operacionais do Azure Cosmos DB diretamente usando conectores separados hoje, ou

  * Se você estiver executando processos de ETL para extrair dados operacionais em um sistema de análise separado.
 
Nesses casos, o Link do Synapse fornece uma experiência de análise mais integrada sem afetar a taxa de transferência provisionada do repositório transacional.

O Link do Synapse não será recomendado se você estiver procurando requisitos de data warehouse de dados tradicionais, como alta simultaneidade, gerenciamento de carga de trabalho e persistência de agregações em várias fontes de dados. Para obter mais informações, consulte [cenários comuns que podem ser capacitados com o Link do Azure Synapse para Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Limitações

* O link Synapse do Azure para Azure Cosmos DB tem suporte para API do SQL e API do Azure Cosmos DB para MongoDB. Não há suporte para a API Gremlin, API do Cassandra e API de Tabela. 

* O repositório analítico só pode ser habilitado para novos contêineres. Para usar o repositório analítico para contêineres existentes, migre dados de seus contêineres existentes para novos contêineres usando [Azure Cosmos DB ferramentas de migração](cosmosdb-migrationchoices.md). Você pode habilitar o link Synapse em contas de Azure Cosmos DB novas e existentes.

* Para os contêineres com o repositório analítico ativado, o backup automático e a restauração de seus dados no repositório analítico não têm suporte no momento. Quando o link do Synapse estiver habilitado em uma conta de banco de dados, Azure Cosmos DB continuará a [fazer backups](./online-backup-and-restore.md) de seus dados automaticamente no armazenamento transacional (somente) de contêineres no intervalo de backup agendado, como sempre. É importante observar que, quando um contêiner com o armazenamento analítico ativado é restaurado para uma nova conta, o contêiner será restaurado somente com armazenamento transacional e nenhum repositório analítico habilitado. 

* O acesso ao repositório analítico do Azure Cosmos DB com o SQL do Synapse não está disponível atualmente.

* No momento, não há suporte para o isolamento de rede para o armazenamento analítico do Azure Cosmso DB, usando pontos de extremidade privados gerenciados no Azure Synapse Analytics.

## <a name="pricing"></a>Preços

O modelo de cobrança do Link do Synapse inclui os custos incorridos usando o repositório analítico do Azure Cosmos DB e o runtime do Synapse. Para saber mais, confira os artigos [Preços do repositório analítico do Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) e [Preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte a seguinte documentação:

* [Visão geral do repositório analítico do Azure Cosmos DB](analytical-store-introduction.md)

* [Introdução ao Link do Azure Synapse para Azure Cosmos DB](configure-synapse-link.md)
 
* [O que é suportado no tempo de execução do Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Perguntas frequentes sobre o Link do Azure Synapse para Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casos de uso do Link do Azure Synapse para Azure Cosmos DB](synapse-link-use-cases.md)
