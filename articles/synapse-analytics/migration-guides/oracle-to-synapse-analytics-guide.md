---
title: 'Oracle para o Azure Synapse Analytics: guia de migração'
description: As seções a seguir fornecem uma visão geral do que está envolvido na migração de uma solução de banco de dados Oracle existente para o Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: bcabb11d67e621ebd2449f2148a0a86ba32d27d9
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419194"
---
# <a name="migration-guide-migrate-oracle-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Guia de migração: migrar o Oracle data warehouse para um pool SQL dedicado no Azure Synapse Analytics
As seções a seguir fornecem uma visão geral do que está envolvido na migração de uma solução existente do Oracle data warehouse para o Azure Synapse Analytics.

## <a name="overview"></a>Visão geral
Antes de migrar, você deve verificar se o Azure Synapse Analytics é a melhor solução para sua carga de trabalho. O Azure Synapse Analytics é um sistema distribuído projetado para executar análises em grandes volumes de dados. Migrar para o Azure Synapse Analytics requer algumas alterações de design que não são difíceis de entender, mas que podem levar algum tempo para implementar. Se sua empresa exige um data warehouse corporativo, os benefícios valem a pena. No entanto, se você não precisar do poder da análise de Synapse do Azure, será mais econômico usar [SQL Server](https://docs.microsoft.com/sql/sql-server/) ou o [banco de dados SQL do Azure](https://docs.microsoft.com/azure/azure-sql/).

Considere o uso do Azure Synapse Analytics quando você:
- Ter um ou mais terabytes de dados.
- Planeje executar análises em quantidades substanciais de dados.
- Precisa da capacidade de dimensionar a computação e o armazenamento.
- Você quer economizar custos pausando recursos de computação quando não precisar deles.

Em vez da análise de Synapse do Azure, considere outras opções para cargas de trabalho operacionais (OLTP) que têm:
- Leituras e gravações de alta frequência.
- Um grande número de seleções singleton.
- Grandes volumes de inserções de linha única.
- Necessidades de processamento linha a linha.
- Formatos incompatíveis (JSON, XML).

## <a name="prerequisites"></a>Pré-requisitos
Para migrar seu data warehouse Oracle para o Azure Synapse Analytics, verifique se você tem os seguintes pré-requisitos: 

- Uma carga de trabalho de data warehouse ou análise 
- O SSMA para Oracle Converta objetos Oracle para SQL Server. Consulte [migrando bancos de dados Oracle para SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) para obter mais informações. 
- Versão mais recente da ferramenta de [caminho de Synapse do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos SQL Server para objetos Synapse do Azure.
- Um [pool SQL dedicado](../get-started-create-workspace.md) no espaço de trabalho Synapse do Azure.  


## <a name="pre-migration"></a>Pré-migração
Depois de tomar a decisão de migrar uma solução existente para o Azure Synapse Analytics, é importante planejar a migração antes de começar. Um objetivo principal do planejamento é garantir que os dados, os esquemas de tabela e o código sejam compatíveis com a análise de Synapse do Azure. Há algumas diferenças de compatibilidade entre o sistema atual e SQL Data Warehouse que você precisará solucionar. Além disso, a migração de grandes quantidades de dados para o Azure leva tempo. O planejamento cuidadoso acelerará o processo de obtenção de seus dados para o Azure. Outro objetivo importante do planejamento é ajustar seu design para garantir que sua solução Aproveite ao máximo o alto desempenho de consulta que o Azure Synapse Analytics foi projetado para fornecer. A criação de data warehouses para escala apresenta padrões de design exclusivos, portanto, as abordagens tradicionais nem sempre são as melhores. Embora alguns ajustes de design possam ser feitos após a migração, fazer alterações no início do processo irá poupar tempo mais tarde.

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
Um dos clientes de bloqueadores críticos enfrentam a tradução de seu código SQL ao migrar de um sistema para outro. O [Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ajudará você a executar uma atualização para uma plataforma moderna do data warehouse, automatizando a conversão de código do data warehouse existente. Ele é uma ferramenta gratuita, intuitiva e fácil de usar que automatiza a conversão de código, permitindo executar uma migração mais rápida para o Azure Synapse Analytics.

## <a name="migrate"></a>Migrar
A execução de uma migração bem-sucedida exige que você migre os esquemas, o código e os dados da tabela. Para obter diretrizes mais detalhadas sobre esses tópicos, consulte:
- O artigo [migra seus esquemas](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- O artigo [migra seu código](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- O artigo [migra seus dados](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>Recursos adicionais 
- O gato (equipe de consultoria para clientes) tem algumas diretrizes excelentes de análise de Synapse do Azure (anteriormente SQL Data Warehouse) publicadas como Postagens de blog. Certifique-se de dar uma olhada em seu artigo, [migrando dados para o Azure SQL data warehouse na prática](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice), para obter diretrizes adicionais sobre a migração.
- Confira o white paper [escolhendo o caminho de migração do banco de dados para o Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) para obter informações e recomendações adicionais.
- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte o artigo [serviço e ferramentas para a migração de dados](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

## <a name="migration-assets-from-real-world-engagements"></a>Ativos de migração de compromissos do mundo real
Para obter assistência adicional com a conclusão deste cenário de migração, consulte os recursos a seguir, que foram desenvolvidos para dar suporte a um compromisso de projeto de migração real.

| Título/link                              | Descrição                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta fornece as melhores plataformas de destino "melhor ajuste", preparação para a nuvem e nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ele oferece um cálculo simples, com um clique e geração de relatórios que ajuda muito a acelerar as avaliações de grandes imóveis, fornecendo um processo de decisão de plataforma de destino automatizado e uniforme. |
| [Tratamento de problemas de codificação de dados ao carregar dados no Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Este blog destina-se a fornecer informações sobre alguns dos problemas de codificação de dados que você pode encontrar ao usar o polybase para carregar dados para SQL Data Warehouse. Este artigo também fornece algumas opções que você pode usar para superar esses problemas e carregar os dados com êxito. |
| [Obtendo tamanhos de tabela no pool do SQL do Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Uma das principais tarefas que um arquiteto deve executar é obter métricas sobre um novo ambiente após a migração: coletando tempos de carregamento do local para a nuvem, coletando tempos de carregamento do polybase, etc. Dessas tarefas, uma das mais importantes é determinar o tamanho do armazenamento em SQL Data Warehouse em comparação com a plataforma atual do cliente. |
| [Utilitário para mover os logons SQL Server locais para o Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Um script do PowerShell que cria um script de comando T-SQL para recriar logons e Selecionar usuários de banco de dados de um SQL Server "local" para um serviço de PaaS do SQL do Azure. A ferramenta permite o mapeamento automático de contas do Windows AD para contas do Azure AD ou pode fazer pesquisas de UPN para cada logon no Windows Active Directory local. A ferramenta também move SQL Server logons nativos. As funções de servidor e banco de dados personalizadas são inseridas em script, bem como associação de função e permissões de usuário e função de banco de dados. Os bancos de dados independentes ainda não têm suporte e apenas um subconjunto de possíveis SQL Server permissões são incluídos no script; ou seja, as permissões Grant com Grant não têm suporte (árvores de permissão complexas). Mais detalhes estão disponíveis no documento de suporte e o script tem comentários para facilitar a compreensão. |

> [!NOTE]
> Esses recursos acima foram desenvolvidos como parte do programa de início de migração de dados (DM JumpStart), que é patrocinado pela equipe de engenharia do grupo de dados do Azure. A principal responsabilidade do DM JumpStart é desbloquear e acelerar as oportunidades complexas de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft. Se você acredita que sua organização estaria interessada em participar do programa DM JumpStart, entre em contato com sua equipe de conta e peça que eles enviem uma indicação.

## <a name="videos"></a>vídeos
- Para obter uma visão geral do guia de migração de banco de dados do Azure e das informações que ele contém, consulte o vídeo [como usar o guia de migração de banco de dados](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/).
- Para obter uma orientação sobre as fases do processo de migração e detalhes sobre as ferramentas e os serviços específicos recomendados para executar a avaliação e a migração, consulte a [visão geral em vídeo da jornada de migração e as ferramentas/serviços recomendados para executar a avaliação e a migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).