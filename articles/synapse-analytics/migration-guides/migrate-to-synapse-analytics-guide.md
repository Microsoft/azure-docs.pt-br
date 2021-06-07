---
title: 'Azure Synapse Analytics: guia de migração'
description: Siga este guia para migrar bancos de dados para um pool de SQL dedicado do Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565637"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrar um data warehouse para um pool de SQL dedicado no Azure Synapse Analytics

As seções a seguir apresentam uma visão geral da migração de soluções de data warehouse para um pool de SQL dedicado do Azure Synapse Analytics.

## <a name="overview"></a>Visão geral

Antes de começar a migração, você deve verificar se o Azure Synapse Analytics é a melhor solução para sua carga de trabalho. O Azure Synapse Analytics é um sistema distribuído que realiza análises em grandes quantidades de dados. A migração para o Azure Synapse Analytics exige algumas mudanças no design, que não são difíceis de entender, mas sua implementação pode demorar um pouco. Se sua empresa exige um data warehouse corporativo, os benefícios valem a pena. No entanto, se você não precisa dos recursos do Azure Synapse Analytics, é mais econômico usar o [SQL Server](https://docs.microsoft.com/sql/sql-server/) ou o [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/azure-sql/).

Considere usar o Azure Synapse Analytics quando você:

- Tiver um ou mais terabytes de dados.
- Planejar executar análise em grandes quantidades de dados.
- Precisar dimensionar a computação e o armazenamento.
- Quiser economizar nos custos pausando os recursos de computação quando não precisar deles.

Em vez do Azure Synapse Analytics, considere outras opções para cargas de trabalho operacionais (OLTP) que têm:

- Alta frequência de leituras e gravações.
- Grande quantidade de seleções singleton.
- Grande volume de inserções de linha única.
- Exigência de processamento linha por linha.
- Formatos incompatíveis (por exemplo, JSON e XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Uma das barreiras críticas que os clientes enfrentam ao migrar entre sistemas é a conversão dos objetos do banco de dados. O [Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ajuda a atualizar para uma plataforma moderna de data warehouse por meio da conversão automatizada dos objetos atuais do data warehouse. Ele é uma ferramenta gratuita, intuitiva e fácil de usar que automatiza a conversão de código, agilizando a migração para o Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="migrate-from-sql-server"></a>[Migrar do SQL Server](#tab/migratefromSQLServer)

Para migrar um data warehouse do SQL Server para o Azure Synapse Analytics, você precisa cumprir estes pré-requisitos:

- Ter uma carga de trabalho de data warehouse ou de análise.
- Baixar a versão mais recente do [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos do SQL Server para objetos do Azure Synapse.
- Ter um [pool de SQL dedicado](../get-started-create-workspace.md) em um workspace do Azure Synapse.

# <a name="migrate-from-netezza"></a>[Migrar do Netezza](#tab/migratefromNetezza)

Para migrar um data warehouse do Netezza para o Azure Synapse Analytics, você precisa cumprir estes pré-requisitos:

- Baixar a versão mais recente do [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos do SQL Server para objetos do Azure Synapse.
- Ter um [pool de SQL dedicado](../get-started-create-workspace.md) em um workspace do Azure Synapse.

Veja mais informações em [Soluções e migração do Azure Synapse Analytics para Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrar do Snowflake](#tab/migratefromSnowflake)

Para migrar um data warehouse do Snowflake para o Azure Synapse Analytics, você precisa cumprir estes pré-requisitos:

- Baixar a versão mais recente do [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos do Snowflake para objetos do Azure Synapse.
- Ter um [pool de SQL dedicado](../get-started-create-workspace.md) em um workspace do Azure Synapse.

# <a name="migrate-from-oracle"></a>[Migrar do Oracle](#tab/migratefromOracle)

Para migrar um data warehouse do Oracle para o Azure Synapse Analytics, você precisa cumprir estes pré-requisitos:

- Ter uma carga de trabalho de data warehouse ou de análise.
- Baixar o Assistente de Migração do Microsoft SQL Server para Oracle a fim de converter os objetos do Oracle para o SQL Server. Veja mais informações em [Migração de bancos de dados Oracle para o SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Baixar a versão mais recente do [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=102787) para migrar objetos do SQL Server para objetos do Azure Synapse.
- Ter um [pool de SQL dedicado](../get-started-create-workspace.md) em um workspace do Azure Synapse.

Veja mais informações em [Soluções e migração do Azure Synapse Analytics para um Oracle data warehouse](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Pré-migração

Depois de decidir migrar a solução atual para o Azure Synapse Analytics, você precisa planejar a migração antes de começar. Uma das principais metas do planejamento é garantir que os dados, os esquemas de tabela e o código sejam compatíveis com o Azure Synapse Analytics. Há algumas diferenças de compatibilidade entre seu sistema atual e o Azure Synapse Analytics, que você precisará solucionar. Além disso, a migração de grandes quantidades de dados para o Azure leva tempo. O planejamento cuidadoso agiliza a migração dos dados para o Azure.

Outra meta do planejamento é ajustar o design para que sua solução aproveite ao máximo o alto desempenho de consulta que o Azure Synapse Analytics oferece. O design de data warehouses para larga escala apresenta padrões de design diferentes. Portanto, as abordagens tradicionais nem sempre são as melhores. É possível ajustar o design após a migração, mas a antecipação das mudanças economiza tempo mais adiante.

## <a name="migrate"></a>Migrações

Para fazer a migração, é preciso migrar os esquemas de tabela, o código e os dados. Veja diretrizes mais detalhadas sobre esses tópicos nos seguintes artigos:

- [Considere o design da tabela](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Considere alteração de código](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Migrar seus dados](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Considere o gerenciamento de carga de trabalho](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Mais recursos

A Equipe de Consultoria para Clientes publicou excelentes postagens de blog com diretrizes sobre o Azure Synapse Analytics (antigo Data Warehouse do SQL do Azure). Veja mais informações sobre migração em [Migração de dados para o Data Warehouse do SQL do Azure na prática](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Ativos de migração de projetos reais

Para obter mais assistência a fim de concluir esse cenário de migração, confira os recursos a seguir. Eles foram desenvolvidos com base em um compromisso de projeto de migração no mundo real.

| Título/link                              | Descrição                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e Ferramenta de Avaliação de Carga de Trabalho de Dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo ou banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado. |
| [Como lidar com problemas de codificação ao carregar dados no Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Esta postagem de blog traz informações sobre alguns dos problemas de codificação de dados que você pode encontrar ao usar o PolyBase para carregar dados no Data Warehouse de SQL. O artigo também apresenta algumas opções para resolver esses problemas e carregar os dados com êxito. |
| [Como obter tamanhos de tabela no pool de SQL dedicado do Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Uma das principais tarefas que um arquiteto deve fazer é obter métricas sobre o novo ambiente após a migração. Por exemplo, coletar o tempo de carregamento do local para a nuvem ou o tempo de carregamento do PolyBase. Uma das tarefas mais importantes é determinar o tamanho do armazenamento no Data Warehouse de SQL em comparação com a plataforma atual do cliente. |
| [Utilitário para mover os logons do SQL Server local para o Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Um script do PowerShell cria um script de comando T-SQL para recriar logons e selecionar usuários do banco de dados de uma instância local do SQL Server para um serviço de PaaS (plataforma como serviço) do SQL do Azure. A ferramenta faz o mapeamento automático das contas do Windows Server Active Directory para contas do Azure Active Directory ou faz pesquisas de UPN para cada logon no Windows Server Active Directory local. A ferramenta também pode mover logons nativos do SQL Server. As funções personalizadas de servidor e de banco de dados são inseridas em script, juntamente com a associação de função, a função do banco de dados e as permissões de usuário. Não há suporte para bancos de dados independentes, e apenas um subconjunto de possíveis permissões do SQL Server é incluído no script. Mais informações estão disponíveis no documento de suporte, e o script tem comentários para facilitar a compreensão. |

A equipe de engenharia de dados do SQL desenvolveu esses recursos. A principal responsabilidade dessa equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Microsoft Azure.

## <a name="videos"></a>Vídeos

Veja como o [Walgreens migrou um sistema de inventário de varejo](https://www.youtube.com/watch?v=86dhd8N1lH4), com cerca de 100 TB de dados, do Netezza para o Azure Synapse Analytics em tempo recorde.
