---
title: 'SQL Server ao banco de dados SQL: visão geral da migração'
description: Saiba mais sobre as diferentes ferramentas e opções disponíveis para migrar seus bancos de dados do SQL Server para o Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 733b2375a26b0157f88bc148b52932e2f3e3f2e2
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488266"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>Visão geral da migração: SQL Server para o banco de dados SQL
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Saiba mais sobre as diferentes opções de migração e considerações para migrar seu SQL Server para o banco de dados SQL do Azure. 

Você pode migrar SQL Server em execução no local ou em: 

- SQL Server em máquinas virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de banco de dados relacional do Amazon (AWS RDS) 
- Mecanismo de computação (Google Cloud Platform-GCP)  
- SQL de nuvem para SQL Server (Google Cloud Platform – GCP) 

Para outros cenários, consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Visão geral

O [banco de dados SQL do Azure](../../database/sql-database-paas-overview.md) é uma opção de destino recomendada para cargas de trabalho SQL Server que exigem uma PaaS (plataforma como serviço) totalmente gerenciada. O banco de dados SQL lida com a maioria das funções de gerenciamento de banco de dados, juntamente com alta disponibilidade, processamento inteligente de consultas, escalabilidade e recursos de desempenho internos para atender a muitos tipos diferentes de aplicativos. 

O banco de dados SQL fornece flexibilidade com vários [modelos de implantação](../../database/sql-database-paas-overview.md#deployment-models) e [camadas de serviço](../../database/service-tiers-vcore.md#service-tiers) que atendem a diferentes tipos de aplicativos ou cargas de trabalho.

Um dos principais benefícios da migração para o banco de dados SQL é que você pode modernizar seu aplicativo aproveitando os recursos de PaaS e eliminar qualquer dependência em componentes técnicos com escopo no nível da instância, como trabalhos do SQL Agent.

Você também pode economizar em custos migrando suas licenças do SQL Server local para o banco de dados SQL do Azure usando o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para SQL Server se você escolher o [modelo de compra baseado em vCore](../../database/service-tiers-vcore.md).

Este guia tem como objetivo esclarecer as opções e considerações de migração à medida que você se prepara para migrar seus bancos de dados do SQL Server para o Azure SQL Database.  

## <a name="considerations"></a>Considerações 

Os principais fatores a serem considerados ao avaliar as opções de migração dependem: 

- Número de servidores e bancos de dados
- Tamanho dos bancos de dados
- Tempo de inatividade de negócios aceitável durante o processo de migração 

As opções de migração listadas neste guia levam esses fatores em conta. Para a migração de dados lógicos para o Azure SQL Database, a hora de migrar pode depender do número de objetos em um banco de dados e do tamanho do banco de dados. 

Diferentes ferramentas estão disponíveis para diferentes cargas de trabalho e preferências do usuário. Algumas ferramentas podem ser usadas para executar uma migração rápida de um banco de dados individual usando uma ferramenta baseada na interface do usuário, enquanto outras podem migrar vários bancos de dados que podem ser automatizados para lidar com as migrações em escala. 

## <a name="choose-appropriate-target"></a>Escolha o destino apropriado

Considere as diretrizes gerais para ajudá-lo a escolher o modelo de implantação certo e a camada de serviço do banco de dados SQL do Azure. Você pode escolher recursos de computação e armazenamento durante a implantação e, em seguida, alterá-los posteriormente usando o  [portal do Azure](../../database/scale-resources.md)  sem incorrer em tempo de inatividade para seu aplicativo.


**Modelos de implantação**: entenda a carga de trabalho do aplicativo e o padrão de uso para decidir entre um único banco de dados ou pool elástico. 

- Um [banco de dados individual](../../database/single-database-overview.md) representa um banco de dados totalmente gerenciado adequado para a maioria dos aplicativos e microserviços de nuvem modernos.
- Um [pool elástico](../../database/elastic-pool-overview.md) é uma coleção de bancos de dados individuais com um conjunto compartilhado de recursos, como CPU ou memória, e adequado para combinar bancos de dados em um pool com padrões de uso previsíveis que podem compartilhar efetivamente o mesmo conjunto de recursos.

**Modelos de compra**: escolha entre o modelo de compra VCORE, DTU ou sem servidor. 

- O [modelo vCore](../../database/service-tiers-vcore.md) permite que você escolha o número de vCores para o banco de dados SQL do Azure, tornando-o a opção mais fácil ao traduzir do SQL Server local. Essa é a única opção que dá suporte ao salvamento de custo de licença com o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- O [modelo de DTU](../../database/service-tiers-dtu.md) abstrai a computação subjacente, a memória e os recursos de e/s para fornecer um DTU combinado. 
- O [modelo sem servidor](../../database/serverless-tier-overview.md) destina-se a cargas de trabalho que exigem dimensionamento automático sob demanda com recursos de computação cobrados por segundo de uso. A camada de computação sem servidor pausa automaticamente os bancos de dados durante períodos inativos (onde apenas o armazenamento é cobrado) e retoma automaticamente os bancos de dados quando a atividade retorna. 

**Camadas de serviço**: escolha entre três camadas de serviço projetadas para diferentes tipos de aplicativos.

- A [camada de serviço do uso geral/Standard](../../database/service-tier-general-purpose.md) oferece uma opção equilibrada voltada para o orçamento com computação e armazenamento adequados para fornecer aplicativos de camada inferior, com redundância interna na camada de armazenamento para se recuperar de falhas. Projetado para a maioria das cargas de trabalho de banco de dados. 
- A [camada de serviço comercialmente crítico/Premium](../../database/service-tier-business-critical.md) é para aplicativos de camada superior que exigem altas taxas de transação, e/s de baixa latência e um alto nível de resiliência com réplicas secundárias disponíveis para failover e para descarregar cargas de trabalho de leitura.
- A [camada de serviço de hiperescala](../../database/service-tier-hyperscale.md) destina-se a bancos de dados que têm volumes de armazenamento cada vez maiores e precisam ser escalados automaticamente para o tamanho do banco de dado de 100 TB. Projetado para bancos de dados muito grandes. 

> [!IMPORTANT]
> A [taxa do log de transações é regida](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) no banco de dados SQL do Azure para limitar altas taxas de ingestão. Dessa forma, durante a migração, pode ser necessário dimensionar os recursos do banco de dados de destino (vCores/DTUs) para facilitar a pressão sobre a CPU ou a taxa de transferência. Escolha o banco de dados de destino de tamanho adequado, mas planeje dimensionar os recursos para a migração, se necessário. 


### <a name="sql-server-on-azure-vm-alternative"></a>Alternativa SQL Server na VM do Azure

Sua empresa pode ter requisitos que fazem [SQL Server em máquinas virtuais do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) um destino mais adequado do que o banco de dados SQL do Azure. 

Se o seguinte se aplicar ao seu negócio, considere migrar para um SQL Server na VM do Azure: 

- Se você precisar de acesso direto ao sistema operacional ou sistema de arquivos, como instalar agentes de terceiros ou personalizados na mesma máquina virtual com SQL Server. 
- Se você tiver uma dependência estrita dos recursos que ainda não têm suporte, como FileStream/Filetable, polybase e transações de instância cruzada. 
- Se você realmente precisa permanecer em uma versão específica do SQL Server (2012, por exemplo). 
- Se os requisitos de computação forem muito menores do que as ofertas de instância gerenciada (um vCore, por exemplo) e a consolidação de banco de dados não for uma opção aceitável. 


## <a name="migration-tools"></a>Ferramentas de migração 

As ferramentas recomendadas para migração são o Assistente de Migração de Dados e o serviço de migração de banco de dados do Azure. Também há outras opções de migração alternativas disponíveis. 

### <a name="recommended-tools"></a>Ferramentas recomendadas

A tabela a seguir lista as ferramentas de migração recomendadas: 

|Tecnologia | Descrição|
|---------|---------|
| [Migrações para Azure](/azure/migrate/how-to-create-azure-sql-assessment) | As migrações para Azure para SQL do Azure permitem que você descubra e avalie seu espaço de dados SQL em escala quando estiver no VMware, fornecendo recomendações de implantação do Azure SQL, dimensionamento de destino e estimativas mensais. | 
|[AMD (Assistente de Migração de Dados)](/sql/dma/dma-migrateonpremsqltosqldb)|O Assistente de Migração de Dados é uma ferramenta de área de trabalho que fornece avaliações diretas de SQL Server e migrações para o banco de dados SQL do Azure (tanto o esquema quanto o Data). A ferramenta pode ser instalada em um servidor local ou no computador que tem conectividade com seus bancos de dados de origem. O processo de migração é uma movimentação de dados lógica entre objetos no banco de dados de origem e de destino. </br> -Migrar bancos de dados individuais (esquema e data)|
|[DMS (Serviço de Migração de Banco de Dados do Azure)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Um serviço do Azure de terceiros que pode migrar seus bancos de dados do SQL Server para o Azure SQL usando o portal do Azure ou automatizado com o PowerShell. O Azure DMS exige que você selecione uma rede virtual (VNet) do Azure preferencial durante o provisionamento para garantir que haja conectividade com seus bancos de dados de SQL Server de origem. </br> -Migre bancos de dados individuais ou em escala. |
| | |


### <a name="alternative-tools"></a>Ferramentas alternativas

A tabela a seguir lista as ferramentas de migração alternativas: 

|Tecnologia |Descrição  |
|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)|Replique dados de fontes de SQL Server de origem para o banco de dado SQL fornecendo uma opção de migração de tipo Publicador de assinante, mantendo a consistência transacional. As alterações de dados incrementais são propagadas para os assinantes à medida que ocorrem nos Publicadores.|
|[Importar serviço de exportação/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) é um arquivo do Windows com uma `.bacpac` extensão que encapsula o esquema e os dados de um banco de dado. O BACPAC pode ser usado para exportar dados de um SQL Server de origem e para importar os dados para o Azure SQL Database. O arquivo BACPAC pode ser importado para um novo banco de dados SQL do Azure usando o portal do Azure. </br></br> Para escala e desempenho com grandes tamanhos de bancos de dados ou um grande número de bancos de dados, você deve considerar o uso do utilitário de linha de comando [SqlPackage](../../database/database-import.md#using-sqlpackage) para exportar e importar bancos de dados.|
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|O [utilitário bcp (programa de cópia em massa)](/sql/tools/bcp-utility) copia dados de uma instância do SQL Server em um arquivo de dados. Use o utilitário BCP para exportar os dados de sua origem e importar o arquivo de dados para o banco de dado SQL de destino. </br></br> Para operações de cópia em massa de alta velocidade a fim de mover dados para o Azure SQL Database, a [ferramenta de cópia em massa inteligente](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) pode ser usada para maximizar a velocidade de transferência aproveitando as tarefas de cópia paralelas.|
|[ADF (Azure Data Factory)](../../../data-factory/connector-azure-sql-database.md)|A [atividade de cópia](../../../data-factory/copy-activity-overview.md) no Azure data Factory migra dados de fontes de SQL Server de origem para o banco de dados SQL usando conectores internos e uma [Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> O ADF dá suporte a uma ampla variedade de [conectores](../../../data-factory/connector-overview.md) para mover dados de fontes SQL Server para o banco de dados SQL.|
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|O Sincronização de Dados SQL é um serviço criado no banco de dados SQL do Azure que permite sincronizar o bi-Directional em vários bancos de dado, tanto localmente quanto na nuvem.</br>A sincronização de dados é útil nos casos em que os dados precisam ser mantidos atualizados em vários bancos de dados no banco de dados SQL do Azure ou SQL Server.|
| | |

## <a name="compare-migration-options"></a>Comparar opções de migração

Compare as opções de migração para escolher o caminho apropriado às suas necessidades de negócios. 

### <a name="recommended-options"></a>Opções recomendadas

A tabela a seguir compara as opções de migração recomendadas: 

|Opção de migração  |Quando usar  |Considerações  |
|---------|---------|---------|
|[AMD (Assistente de Migração de Dados)](/sql/dma/dma-migrateonpremsqltosqldb) | -Migrar bancos de dados individuais (esquema e data).  </br> -Pode acomodar o tempo de inatividade durante o processo de migração de dados. </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM | -A atividade de migração executa a movimentação de dados entre objetos de banco de dados (da origem para o destino) e, portanto, recomenda-se a execução fora dos horários de pico. </br> -O DMA relata o status da migração por objeto de banco de dados, incluindo o número de linhas migradas.  </br> -Para grandes migrações (número de bancos de dados/tamanho do banco de dados), use o serviço de migração de banco de dados do Azure listado abaixo.|
|[DMS (Serviço de Migração de Banco de Dados do Azure)](../../../dms/tutorial-sql-server-to-azure-sql.md)| -Migre bancos de dados individuais ou em escala. </br> -Pode acomodar o tempo de inatividade durante o processo de migração. </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM | -As migrações em escala podem ser automatizadas por meio do [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Tempo para concluir a migração depende do tamanho do banco de dados e do número de objetos no banco de dados. </br> -Requer que o banco de dados de origem seja definido como somente leitura. |
| | | |

### <a name="alternative-options"></a>Opções alternativas

A tabela a seguir compara as opções de migração alternativas: 

|Método/tecnologia |Quando usar    |Considerações  |
|---------|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)| – Migre continuamente publicando alterações de tabelas de banco de dados de origem para tabelas de banco de dados SQL de destino. </br> -Migrações de banco de dados completas ou parciais de tabelas selecionadas (subconjunto de banco de dados).  </br> </br> Fontes com suporte: </br> - [SQL Server (2016-2019) com algumas limitações](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP computação SQL Server VM  | -A instalação é relativamente complexa em comparação com outras opções de migração.   </br> -Fornece uma opção de replicação contínua para migrar dados (sem colocar os bancos de dados offline).  </br> -A replicação transacional tem várias limitações a serem consideradas ao configurar o Publicador no SQL Server de origem. Confira [limitações na publicação de objetos](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) para saber mais. </br>-É possível monitorar a [atividade de replicação](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Importar serviço de exportação/BACPAC](../../database/database-import.md)| – Migre bancos de dados individuais de aplicativos de linha de negócios. </br>-Adequado para bancos de dados menores.  </br>  -Não requer uma ferramenta ou um serviço de migração separado. </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM  |  -Requer tempo de inatividade, pois os dados precisam ser exportados na origem e importados no destino.   </br> -Os formatos de arquivo e os tipos de dados usados na exportação/importação precisam ser consistentes com os esquemas de tabela para evitar erros de incompatibilidade de tipo de dados/truncamento.  </br> -Tempo necessário para exportar um banco de dados com um grande número de objetos pode ser significativamente maior.       |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Migrar migrações de dados completas ou parciais. </br> -Pode acomodar o tempo de inatividade. </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM   | -Requer tempo de inatividade para exportar dados da origem e importar para o destino. </br> -Os formatos de arquivo e os tipos de dados usados na exportação/importação precisam ser consistentes com os esquemas de tabela. |
|[ADF (Azure Data Factory)](../../../data-factory/connector-azure-sql-database.md)| -Migrar e/ou transformar dados de bancos de dado do SQL Server de origem. </br> -Mesclagem de dados de várias fontes de dados para o Azure SQL Database, normalmente para cargas de trabalho de BI (Business Intelligence).  |  -Requer a criação de pipelines de movimentação de dados no ADF para mover dados da origem para o destino.   </br> - O [custo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) é uma consideração importante e é baseado nos gatilhos de pipeline, execuções de atividade, duração da movimentação de dados, etc. |
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Sincronizar dados entre bancos de dado de origem e de destino.</br> -Adequado para executar a sincronização contínua entre o banco de dados SQL do Azure e o SQL Server local em um fluxo bidirecional. | -O banco de dados SQL do Azure deve ser o banco de dados de Hub para sincronização com o banco de dados local SQL Server como banco de dados membro.</br> – Em comparação com a replicação transacional, o Sincronização de Dados SQL dá suporte à sincronização de dados bidirecional entre o local e o Azure SQL Database. </br> -Pode ter um impacto maior no desempenho dependendo da carga de trabalho.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilidade de recursos 

Há considerações adicionais ao migrar cargas de trabalho que dependem de outros recursos de SQL Server.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migre pacotes SQL Server Integration Services (SSIS) para o Azure reimplantando os pacotes no tempo de execução do Azure-SSIS no [Azure data Factory](../../../data-factory/introduction.md). Azure Data Factory [dá suporte à migração de pacotes do SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) , fornecendo um tempo de execução criado para executar pacotes do SSIS no Azure. Como alternativa, você também pode reescrever nativamente a lógica ETL do SSIS no ADF usando [fluxos](../../../data-factory/concepts-data-flow-overview.md)de os.


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrar relatórios do SQL Server Reporting Services (SSRS) para relatórios paginados no Power BI. Use a [ferramenta de migração RDL](https://github.com/microsoft/RdlMigration) para ajudar a preparar e migrar seus relatórios. Essa ferramenta foi desenvolvida pela Microsoft para ajudar os clientes a migrar relatórios RDL de seus servidores do SSRS para o Power BI. Ele está disponível no GitHub e documenta uma explicação completa do cenário de migração. 

#### <a name="high-availability"></a>Alta disponibilidade
A configuração manual de SQL Server recursos de alta disponibilidade como Always On instâncias de cluster de failover e grupos de disponibilidade Always On se torna obsoleta no banco de dados SQL do Azure de destino como uma arquitetura de alta disponibilidade já está incorporada ao banco de dados SQL [uso geral (modelo de disponibilidade padrão)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [comercialmente crítico (modelo de disponibilidade Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) . A camada de serviço Comercialmente Crítico/Premium também fornece expansão de leitura que permite a conexão em um dos nós secundários para fins somente leitura. 

Além da arquitetura de alta disponibilidade incluída no banco de dados SQL, também há o recurso [grupos de failover automático](../../database/auto-failover-group-overview.md) que permite gerenciar a replicação e o failover de bancos de dados em uma instância gerenciada para outra região. 

#### <a name="sql-agent-jobs"></a>Trabalhos do SQL Agent
Os trabalhos do SQL Agent não têm suporte direto no banco de dados SQL do Azure e precisarão ser implantados em [trabalhos de banco de dados elástico (versão prévia)](../../database/job-automation-overview.md).

#### <a name="logins-and-groups"></a>Logons e grupos
Mova logons do SQL do SQL Server de origem para o banco de dados SQL do Azure usando o serviço de migração de banco de dados (DMS) no modo offline.  Use a folha de **logons selecionados** no **Assistente de migração** para migrar logons para o banco de dados SQL de destino. 

Os usuários e grupos do Windows também podem ser migrados usando o DMS habilitando o botão de alternância correspondente na página de configuração do DMS. 

Como alternativa, você pode usar a [ferramenta do utilitário do PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) especialmente projetada pelos arquitetos de migração de dados da Microsoft. O utilitário usa o PowerShell para criar um script Transact-SQL (T-SQL) para recriar logons e Selecionar usuários de banco de dados da origem para o destino. A ferramenta mapeia automaticamente as contas do Windows AD para as contas do Azure AD e pode fazer uma pesquisa de UPN para cada logon no Active Directory de origem. A ferramenta scripts de servidor personalizado e funções de banco de dados, bem como associação de função, função de banco de dados e permissões de usuário. Os bancos de dados independentes ainda não têm suporte e apenas um subconjunto de possíveis SQL Server permissões são incluídos no script. 


#### <a name="system-databases"></a>Bancos de dados do sistema
Para o banco de dados SQL do Azure, os únicos bancos de dados do sistema aplicáveis são [Master](/sql/relational-databases/databases/master-database) e tempdb. Para saber mais, confira [tempdb no banco de dados SQL do Azure](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Aproveite os recursos avançados 

Não se esqueça de aproveitar os recursos avançados baseados em nuvem oferecidos pelo banco de dados SQL. Por exemplo, você não precisa mais se preocupar com o gerenciamento de backups, pois o serviço faz isso para você. Você pode restaurar para qualquer [ponto no tempo dentro do período de retenção](../../database/recovery-using-backups.md#point-in-time-restore). 

Para reforçar a segurança, considere o uso de [Azure Active Directory autenticação](../../database/authentication-aad-overview.md), [auditoria](../../database/auditing-overview.md), [detecção de ameaças](../../database/azure-defender-for-sql.md), [segurança em nível de linha](/sql/relational-databases/security/row-level-security)e máscara de [dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking).

Além dos recursos avançados de gerenciamento e segurança, o banco de dados SQL fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorar e ajustar sua carga de trabalho](../../database/monitor-tune-overview.md). [Análise de SQL do Azure (versão prévia)](../../../azure-monitor/insights/azure-sql.md) é uma solução de monitoramento de nuvem avançada para monitorar o desempenho de todos os seus bancos de dados no banco de dados SQL do Azure em escala e em várias assinaturas em uma única exibição. Análise de SQL do Azure coleta e visualiza as principais métricas de desempenho com inteligência interna para solução de problemas de desempenho.

[Ajuste automático](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   monitora continuamente o desempenho de suas estatísticas do plano de execução do SQL e corrige automaticamente os problemas de desempenho identificados. 


## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional, consulte os recursos a seguir que foram desenvolvidos para projetos de migração do mundo real.

|Ativo  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ele oferece cálculo simples, com um clique e geração de relatórios que ajuda a acelerar avaliações de grandes imóveis, fornecendo um processo de decisão de plataforma de destino automatizado e uniforme.|
|[Utilitário DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|O DBLoader pode ser usado para carregar dados de arquivos de texto delimitados em SQL Server. Este utilitário de console do Windows usa o SQL Server interface carregamento em massa Native Client, que funciona em todas as versões do SQL Server, incluindo o banco de dados SQL do Azure.|
|[Criação de banco de dados em massa com o PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Isso inclui um conjunto de três scripts do PowerShell que criam um grupo de recursos (create_rg.ps1), o [servidor lógico no Azure](../../database/logical-servers.md) (create_sqlserver.ps1) e o banco de dados SQL do azure (create_sqldb.ps1). Os scripts incluem recursos de loop para que você possa iterar e criar quantos servidores e bancos de dados forem necessários.|
|[Implantação de esquema em massa com MSSQL-Scripter & PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Esse ativo cria um grupo de recursos, um ou vários [servidores lógicos no Azure](../../database/logical-servers.md) para hospedar o banco de dados SQL do Azure, exporta todos os esquemas de um SQL Server local (ou vários SQL Servers (2005 +) e os importa para o banco de dados SQL do Azure.|
|[Converter SQL Server Agent trabalhos em trabalhos de banco de dados elástico](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Esse script migra seus trabalhos de SQL Server Agent de origem para trabalhos de banco de dados elástico.|
|[Enviar emails do banco de dados SQL do Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Isso fornece uma solução como uma alternativa à capacidade do SendMail que está disponível no SQL Server local. A solução usa Azure Functions e o serviço SendGrid do Azure para enviar emails do banco de dados SQL do Azure.|
|[Utilitário para mover os logons SQL Server locais para o banco de dados SQL do Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Um script do PowerShell que cria um script de comando T-SQL para recriar logons e Selecionar usuários de banco de dados local SQL Server para o banco de dados SQL do Azure. A ferramenta permite o mapeamento automático de contas do Windows AD para contas do Azure AD, bem como a migração opcional de SQL Server logons nativos.|
|[Automação de coleta de dados de PerfMon usando logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Uma ferramenta que coleta dados de PerMidade para entender o desempenho da linha de base e ajuda nas recomendações de destino de migração. Essa ferramenta usa logman.exe para criar o comando que irá criar, iniciar, parar e excluir contadores de desempenho definidos em um SQL Server remoto|
|[White Paper-migração de banco de dados para o BD SQL do Azure usando BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Este white paper fornece orientações e etapas para ajudar a acelerar as migrações de SQL Server para o banco de dados SQL do Azure usando arquivos BACPAC.|

Esses recursos foram desenvolvidos como parte do programa Data SQL Ninja, que é patrocinado pela equipe de engenharia do Grupo de Dados do Azure. A principal responsabilidade do programa Data SQL Ninja é desbloquear e acelerar as oportunidades complexas e diversas de migração da plataforma de dados para a plataforma de Dados do Azure da Microsoft. Se você acredita que sua organização tem interesse em participar do programa Data SQL Ninja, entre em contato com sua equipe de contas e peça que eles enviem uma indicação.


## <a name="next-steps"></a>Próximas etapas

Para iniciar a migração de seu SQL Server para o banco de dados SQL do Azure, consulte o [Guia de migração do SQL Server para o banco de dados SQL](sql-server-to-sql-database-guide.md).

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o banco de dados SQL, consulte:
   - [Visão geral do banco de dados SQL do Azure](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).