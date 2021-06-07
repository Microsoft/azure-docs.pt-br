---
title: 'SQL Server para o Banco de Dados SQL do Azure: visão geral da migração'
description: Saiba mais sobre as ferramentas e as opções disponíveis para migrar seus bancos de dados do SQL Server para o Banco de Dados SQL do Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065133"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Visão geral da migração: SQL Server para o Banco de Dados SQL do Azure
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Conheça as opções e as considerações para migrar seus bancos de dados do SQL Server para o Banco de Dados SQL do Azure. 

Você pode migrar bancos de dados do SQL Server em execução no local ou nos seguintes: 

- SQL Server em Máquinas Virtuais do Azure.  
- AWS (Amazon Web Services) EC2 (Elastic Compute Cloud).
- AWS RDS (Relational Database Service).
- Compute Engine na GCP (Google Cloud Platform).  
- Cloud SQL para SQL Server na GCP. 

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Visão geral

O [Banco de Dados SQL do Azure](../../database/sql-database-paas-overview.md) é uma opção de destino recomendada para cargas de trabalho do SQL Server que exigem uma PaaS (plataforma como serviço) totalmente gerenciada. O Banco de Dados SQL cuida da maioria das funções de gerenciamento de banco de dados. Ele também tem alta disponibilidade interna, processamento inteligente de consultas, escalabilidade e funcionalidades de desempenho para se adaptar a muitos tipos de aplicativos. 

O Banco de Dados SQL fornece flexibilidade com vários [modelos de implantação](../../database/sql-database-paas-overview.md#deployment-models) e [camadas de serviço](../../database/service-tiers-vcore.md#service-tiers) que atendem a diferentes tipos de aplicativos ou cargas de trabalho.

Um dos principais benefícios da migração para o Banco de Dados SQL é que você pode modernizar seu aplicativo usando as funcionalidades de PaaS. Posteriormente, você pode eliminar qualquer dependência em componentes técnicos com escopo no nível da instância, como os trabalhos do SQL Agent.

Também pode reduzir custos com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para SQL Server a fim de migrar suas licenças locais do SQL Server para o Banco de Dados SQL do Azure. Essa opção estará disponível se você escolher o [modelo de compra baseado em vCore](../../database/service-tiers-vcore.md).

## <a name="considerations"></a>Considerações 

Os principais fatores a serem considerados quando você estiver avaliando as opções de migração são: 

- Número de servidores e bancos de dados
- Tamanho dos bancos de dados
- Tempo de inatividade de negócios aceitável durante o processo de migração 

As opções de migração listadas neste guia levam em conta esses fatores. Para a migração de dados lógicos para o Banco de Dados SQL do Azure, o tempo de migração pode depender do número de objetos em um banco de dados e do tamanho dele. 

Há ferramentas disponíveis para várias cargas de trabalho e preferências do usuário. Algumas ferramentas podem ser usadas para executar uma migração rápida de um banco de dados individual por meio de uma ferramenta baseada em interface do usuário. Outras ferramentas podem automatizar a migração de vários bancos de dados para lidar com migrações em escala. 

## <a name="choose-an-appropriate-target"></a>Escolher um destino apropriado

Considere as diretrizes gerais para ajudar você a escolher o modelo de implantação e a camada de serviço ideais do Banco de Dados SQL do Azure. Você pode escolher recursos de computação e armazenamento durante a implantação e [alterá-los posteriormente usando o portal do Azure](../../database/scale-resources.md) sem gerar tempo de inatividade para seu aplicativo.

**Modelos de implantação**: entenda o padrão de uso e a carga de trabalho do aplicativo para decidir entre um banco de dados individual ou um pool elástico. 

- Um [banco de dados individual](../../database/single-database-overview.md) representa um banco de dados totalmente gerenciado que é adequado para a maioria dos aplicativos e microsserviços de nuvem modernos.
- Um [pool elástico](../../database/elastic-pool-overview.md) é uma coleção de bancos de dados individuais com um conjunto compartilhado de recursos, como CPU ou memória. Ele é adequado para combinar bancos de dados em um pool com padrões de uso previsíveis que podem compartilhar efetivamente o mesmo conjunto de recursos.

**Modelos de compra**: escolha entre os modelos de compra baseados em vCore, DTU (unidade de transação do banco de dados) ou sem servidor. 

- O [modelo baseado em vCore](../../database/service-tiers-vcore.md) permite que você escolha o número de vCores para o Banco de Dados SQL do Azure, sendo, portanto, a opção mais fácil para a conversão do SQL Server local. Essa é a única opção que dá suporte ao salvamento de custos de licença com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- O [modelo de DTU](../../database/service-tiers-dtu.md) elimina os recursos de computação, memória e E/S subjacentes para fornecer uma DTU combinada. 
- O [modelo sem servidor](../../database/serverless-tier-overview.md) destina-se a cargas de trabalho que exigem dimensionamento automático sob demanda com recursos de computação cobrados por segundo de uso. A camada de computação sem servidor pausa os bancos de dados automaticamente durante períodos inativos (quando apenas o armazenamento é cobrado). Ela retoma automaticamente os bancos de dados quando a atividade retorna. 

**Camadas de serviço**: escolha entre três camadas de serviço projetadas para diferentes tipos de aplicativos.

- A [camada de serviço Uso Geral/Standard](../../database/service-tier-general-purpose.md) oferece uma opção balanceada e orientada por orçamento com armazenamento e computação adequados para fornecer aplicativos nas camadas intermediária e inferior. A redundância é incorporada na camada de armazenamento para recuperação de falhas. Ela foi projetada para a maioria das cargas de trabalho de banco de dados. 
- A [camada de serviço Comercialmente Crítico/Premium](../../database/service-tier-business-critical.md) destina-se a aplicativos de alta camada que exigem altas taxas de transação, E/S de baixa latência e um alto nível de resiliência. Há réplicas secundárias disponíveis para failover e para descarregar cargas de trabalho de leitura.
- A [camada de serviço Hiperescala](../../database/service-tier-hyperscale.md) destina-se a bancos de dados que têm volumes de armazenamento cada vez maiores e precisam ser escalados verticalmente de modo automático para até 100 TB. Ela foi projetada para bancos de dados muito grandes. 

> [!IMPORTANT]
> A [taxa do log de transações é controlada](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) no Banco de Dados SQL do Azure para limitar altas taxas de ingestão. Dessa forma, durante a migração, talvez seja necessário escalar os recursos do banco de dados de destino (vCores ou DTUs) para aliviar a pressão sobre a CPU ou a taxa de transferência. Escolha o banco de dados de destino de tamanho adequado, mas planeje a escala vertical de recursos para a migração, se necessário. 


### <a name="sql-server-vm-alternative"></a>Alternativa à VM do SQL Server

Sua empresa pode ter requisitos que façam do [SQL Server em Máquinas Virtuais do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) um destino mais adequado do que o Banco de Dados SQL do Azure. 

Caso uma das seguintes condições se aplique à sua empresa, considere a possibilidade de migração para uma VM (máquina virtual) do SQL Server: 

- Você precisa ter acesso direto ao sistema operacional ou ao sistema de arquivos, por exemplo, para instalar agentes personalizados ou de terceiros na mesma máquina virtual com o SQL Server. 
- Você tem uma dependência estrita de recursos ainda sem suporte, como transações entre instâncias, PolyBase e FileStream/FileTable. 
- Você precisa permanecer em uma versão específica do SQL Server (2012, por exemplo). 
- Seus requisitos de computação são muito menores do que o oferecido pela instância gerenciada (um vCore, por exemplo) e a consolidação de banco de dados não é uma opção aceitável. 


## <a name="migration-tools"></a>Ferramentas de migração 

Recomendamos as seguintes ferramentas de migração: 

|Tecnologia | Descrição|
|---------|---------|
| [Migrações para Azure](../../../migrate/how-to-create-azure-sql-assessment.md) | Este serviço do Azure ajuda você a descobrir e avaliar seu acervo de dados SQL em escala no VMware. Ele fornece recomendações de implantação, dimensionamento de destino e estimativas mensais do SQL do Azure. | 
|[Assistente de migração de dados](/sql/dma/dma-migrateonpremsqltosqldb)|Esta ferramenta da área de trabalho da Microsoft fornece avaliações diretas do SQL Server e migrações de bancos de dados individuais para o Banco de Dados SQL do Azure (de esquema e de dados). </br></br>A ferramenta pode ser instalada em um servidor local ou no computador local que tenha conectividade com seus bancos de dados de origem. O processo de migração é uma movimentação de dados lógica entre objetos nos bancos de dados de origem e de destino.|
|[Serviço de Migração de Banco de Dados do Azure](../../../dms/tutorial-sql-server-to-azure-sql.md)|Esse serviço do Azure pode migrar bancos de dados do SQL Server para o Banco de Dados SQL do Azure por meio do portal do Azure ou automaticamente pelo PowerShell. O Serviço de Migração de Banco de Dados exige que você selecione uma rede virtual do Azure preferencial durante o provisionamento para garantir a conectividade com os bancos de dados do SQL Server de origem. Você pode migrar bancos de dados individuais ou em escala. |
| | |


A seguinte tabela lista as ferramentas de migração alternativas: 

|Tecnologia |Descrição  |
|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)|Replique dados de tabelas de banco de dados de origem do SQL Server para o Banco de Dados SQL do Azure fornecendo uma opção de migração de tipo editor/assinante, mantendo a consistência transacional. As alterações de dados incrementais são propagadas para os assinantes à medida que ocorrem nos publicadores.|
|[Serviço de Importação/Exportação/BACPAC](../../database/database-import.md)|Um [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) é um arquivo do Windows com uma extensão .bacpac que encapsula o esquema e os dados de um banco de dados. Use o BACPAC para exportar dados de uma fonte do SQL Server e importar os dados para o Banco de Dados SQL do Azure. Um arquivo BACPAC pode ser importado para um novo banco de dados SQL por meio do portal do Azure. </br></br> Para aprimorar a escala e o desempenho com tamanhos de bancos de dados grandes ou com muitos bancos de dados, considere o uso da ferramenta de linha de comando [SqlPackage](../../database/database-import.md#using-sqlpackage) para exportar e importar bancos de dados.|
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|A [ferramenta bcp (programa de cópia em massa)](/sql/tools/bcp-utility) copia em massa os dados de uma instância do SQL Server para um arquivo de dados. Use a ferramenta para exportar os dados da origem e importar o arquivo de dados para o banco de dados SQL de destino. </br></br> Para operações de cópia em massa de alta velocidade para mover dados para o Banco de Dados SQL do Azure, use a [ferramenta de Cópia em Massa Inteligente](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) a fim de maximizar a velocidade de transferência aproveitando as tarefas de cópia paralelas.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|A [atividade Copy](../../../data-factory/copy-activity-overview.md) do Azure Data Factory migra os dados de bancos de dados do SQL Server de origem para o Banco de Dados SQL do Azure usando conectores internos e um [runtime de integração](../../../data-factory/concepts-integration-runtime.md).</br> </br> O Data Factory dá suporte a uma ampla variedade de [conectores](../../../data-factory/connector-overview.md) para mover dados de fontes do SQL Server para o Banco de Dados SQL do Azure.|
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|A Sincronização de Dados SQL é um serviço baseado no Banco de Dados SQL do Azure que permite sincronizar os dados selecionados bidirecionalmente entre vários bancos de dados, no local e na nuvem.</br>A Sincronização de Dados é útil nos casos em que os dados precisam ser atualizados em vários bancos de dados no Banco de Dados SQL do Azure ou no SQL Server.|
| | |

## <a name="compare-migration-options"></a>Comparar opções de migração

Compare as opções de migração para escolher o caminho apropriado às suas necessidades empresariais. 

A seguinte tabela compara as opções de migração recomendadas: 

|Opção de migração  |Quando usar  |Considerações  |
|---------|---------|---------|
|[Assistente de migração de dados](/sql/dma/dma-migrateonpremsqltosqldb) | – Migra bancos de dados individuais (de esquema e de dados).  </br> – Pode acomodar algum tempo de inatividade durante o processo de migração de dados. </br> </br> Fontes compatíveis: </br> – SQL Server (2005 a 2019) local ou VM do Azure </br> – AWS EC2 </br> – AWS RDS </br> – VM do SQL Server de computação da GCP | – A atividade de migração executa a movimentação de dados entre objetos de banco de dados (da origem para o destino). Portanto, recomendamos que você a execute fora do horário de pico. </br> – O Assistente de Migração de Dados relata o status da migração por objeto de banco de dados, incluindo o número de linhas migradas.  </br> – Para migrações grandes (número de bancos de dados ou tamanho do banco de dados), use o Serviço de Migração de Banco de Dados do Azure.|
|[Serviço de Migração de Banco de Dados do Azure](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Migra bancos de dados individuais ou em escala. </br> – Pode acomodar algum tempo de inatividade durante o processo de migração. </br> </br> Fontes compatíveis: </br> – SQL Server (2005 a 2019) local ou VM do Azure </br> – AWS EC2 </br> – AWS RDS </br> – VM do SQL Server de computação da GCP | – As migrações em escala podem ser automatizadas por meio do [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> – O tempo de conclusão da migração depende do tamanho do banco de dados e do número de objetos nele. </br> – Exige que o banco de dados de origem seja definido como somente leitura. |
| | | |

A seguinte tabela compara as opções de migração alternativas: 

|Método ou tecnologia |Quando usar    |Considerações  |
|---------|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)| – Faz migrações contínuas publicando alterações de tabelas de banco de dados de origem para tabelas de Banco de Dados SQL de destino. </br> – Faz migrações completas ou parciais de banco de dados de tabelas selecionadas (subconjunto de um banco de dados).  </br> </br> Fontes compatíveis: </br> - [SQL Server (2016 a 2019) com algumas limitações](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> – VM do SQL Server de computação da GCP  | – A instalação é relativamente complexa comparado a outras opções de migração.   </br> – Fornece uma opção de replicação contínua para migrar os dados (sem colocar os bancos de dados offline).  </br> – A replicação transacional tem limitações a serem consideradas quando você está configurando o editor na instância do SQL Server de origem. Confira [Limitações na publicação de objetos](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) para saber mais. </br>– É possível [monitorar a atividade de replicação](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Serviço de Importação/Exportação/BACPAC](../../database/database-import.md)| – Migra bancos de dados individuais de aplicativos de linha de negócios. </br>– Adequado para bancos de dados menores.  </br>  – Não exige uma ferramenta nem um serviço de migração separado. </br> </br> Fontes compatíveis: </br> – SQL Server (2005 a 2019) local ou VM do Azure </br> – AWS EC2 </br> – AWS RDS </br> – VM do SQL Server de computação da GCP  |  – Exige algum tempo de inatividade porque os dados precisam ser exportados na origem e importados no destino.   </br> – Os formatos de arquivo e os tipos de dados usados na exportação ou na importação precisam ser consistentes com os esquemas de tabela para evitar erros de truncamento ou de tipos de dados incompatíveis.  </br> – O tempo necessário para exportar um banco de dados com muitos objetos pode ser significativamente maior.       |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Faz migrações completas ou parciais de dados. </br> – Pode acomodar algum tempo de inatividade. </br> </br> Fontes compatíveis: </br> – SQL Server (2005 a 2019) local ou VM do Azure </br> – AWS EC2 </br> – AWS RDS </br> – VM do SQL Server de computação da GCP   | – Exige algum tempo de inatividade para exportar os dados da origem e importá-los para o destino. </br> – Os formatos de arquivo e os tipos de dados usados na exportação ou na importação precisam ser consistentes com os esquemas de tabela. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| – Migra e/ou transforma dados dos bancos de dados do SQL Server de origem. </br> – A mesclagem de dados de várias fontes de dados para o Banco de Dados SQL do Azure normalmente se destina a cargas de trabalho de BI (business intelligence).  |  – Exige a criação de pipelines de movimentação de dados no Data Factory para mover dados da origem para o destino.   </br> - O [custo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) é uma consideração importante e baseia-se em fatores como gatilhos de pipeline, execuções de atividade e duração da movimentação de dados. |
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| – Sincroniza dados entre bancos de dados de origem e de destino.</br> – Adequado para executar a sincronização contínua entre o Banco de Dados SQL do Azure e o SQL Server local em um fluxo bidirecional. | – O Banco de Dados SQL do Azure precisa ser o banco de dados hub para sincronização com um banco de dados local do SQL Server como um banco de dados membro.</br> – Em comparação com a replicação transacional, a Sincronização de Dados SQL dá suporte à sincronização de dados bidirecional entre o local e o Banco de Dados SQL do Azure. </br> – Pode ter um maior impacto no desempenho, dependendo da carga de trabalho.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilidade de recursos 

Há mais considerações quando você está migrando cargas de trabalho que dependem de outros recursos do SQL Server.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migre pacotes SSIS (SQL Server Integration Services) para o Azure reimplantando-os no runtime do Azure-SSIS no [Azure Data Factory](../../../data-factory/introduction.md). O Azure Data Factory [dá suporte à migração de pacotes SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) fornecendo um runtime criado para executar pacotes SSIS no Azure. Como alternativa, você pode regenerar a lógica ETL do SSIS (extração, transformação e carregamento) nativamente no Azure Data Factory usando [fluxos de dados](../../../data-factory/concepts-data-flow-overview.md).


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migre relatórios do SSRS (SQL Server Reporting Services) para relatórios paginados no Power BI. Use a [Ferramenta de Migração RDL](https://github.com/microsoft/RdlMigration) para ajudar a preparar e migrar seus relatórios. A Microsoft desenvolveu essa ferramenta para ajudar os clientes a migrar relatórios de linguagem RDL dos servidores do SSRS para o Power BI. Ela está disponível no GitHub e é acompanhada por explicação de ponta a ponta do cenário de migração. 

### <a name="high-availability"></a>Alta disponibilidade
A configuração manual dos recursos de alta disponibilidade do SQL Server como instâncias de cluster de failover do Always On e grupos de disponibilidade Always On se torna obsoleta no banco de dados SQL de destino. A arquitetura de alta disponibilidade já está incorporada às camadas de serviço [Uso Geral (modelo de disponibilidade Standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [Comercialmente Crítico (modelo de disponibilidade Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) para o Banco de Dados SQL do Azure. A camada de serviço Comercialmente Crítico/Premium também fornece expansão de leitura que permite a conexão em um dos nós secundários para fins somente leitura. 

Além da arquitetura de alta disponibilidade incluída no Banco de Dados SQL do Azure, o recurso [grupos de failover automático](../../database/auto-failover-group-overview.md) permite que você gerencie a replicação e o failover de bancos de dados em uma instância gerenciada para outra região. 

### <a name="sql-agent-jobs"></a>Trabalhos do SQL Agent
Não há suporte direto para os trabalhos do SQL Agent no Banco de Dados SQL do Azure, e é necessário implantá-los em [trabalhos de banco de dados elástico (versão prévia)](../../database/job-automation-overview.md).

### <a name="logins-and-groups"></a>Logons e grupos
Mova os logons do SQL da fonte do SQL Server para o Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados no modo offline. Use o painel **Logons selecionados** do Assistente de Migração para migrar os logons para o banco de dados SQL de destino. 

Migre também usuários e grupos do Windows por meio do Serviço de Migração de Banco de Dados habilitando a alternância correspondente na página **Configuração** do Serviço de Migração de Banco de Dados. 

Como alternativa, você pode usar o [utilitário do PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) especialmente projetado pelos arquitetos de migração de dados da Microsoft. O utilitário usa o PowerShell para criar um script T-SQL (Transact-SQL) para recriar logons e selecionar usuários de banco de dados da origem para o destino. 

O utilitário do PowerShell mapeia automaticamente as contas do Windows Server Active Directory para as contas do Azure AD (Azure Active Directory) e pode fazer uma pesquisa de UPN para cada logon na instância do Active Directory de origem. O utilitário gera scripts de funções personalizadas de servidor e de banco de dados, junto com a associação de função e as permissões de usuário. Ainda não há suporte para bancos de dados independentes, e apenas um subconjunto de possíveis permissões do SQL Server é incluído no script. 

### <a name="system-databases"></a>Bancos de dados do sistema
Para o Banco de Dados SQL do Azure, os únicos bancos de dados do sistema aplicáveis são [mestre](/sql/relational-databases/databases/master-database) e tempdb. Para saber mais, confira [Tempdb no Banco de Dados SQL do Azure](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Recursos avançados 

Não se esqueça de aproveitar os recursos avançados baseados em nuvem do Banco de Dados SQL. Por exemplo, você não precisa se preocupar em gerenciar backups porque o serviço faz isso para você. Você pode restaurá-los em qualquer [ponto no tempo dentro do período de retenção](../../database/recovery-using-backups.md#point-in-time-restore). 

Para reforçar a segurança, considere o uso da [autenticação do Azure AD](../../database/authentication-aad-overview.md), da [auditoria](../../database/auditing-overview.md), da [detecção de ameaças](../../database/azure-defender-for-sql.md), da [Segurança em Nível de Linha](/sql/relational-databases/security/row-level-security)e da [Máscara Dinâmica de Dados](/sql/relational-databases/security/dynamic-data-masking).

Além dos recursos avançados de gerenciamento e segurança, o Banco de Dados SQL fornece ferramentas que podem ajudar você a [monitorar e ajustar sua carga de trabalho](../../database/monitor-tune-overview.md). A [Análise de SQL do Azure (versão prévia)](../../../azure-monitor/insights/azure-sql.md) é uma solução avançada que monitora o desempenho de todos os seus bancos de dados no Banco de Dados SQL do Azure em escala e em várias assinaturas em uma só exibição. A Análise de SQL do Azure coleta e visualiza as principais métricas de desempenho com inteligência interna para solução de problemas de desempenho.

O [ajuste automático](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) monitora continuamente o desempenho do seu plano de execução do SQL e corrige automaticamente os problemas de desempenho identificados. 


## <a name="migration-assets"></a>Ativos de migração 

Para obter mais assistência, confira os recursos a seguir que foram desenvolvidos para projetos de migração do mundo real.

|Ativo  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Essa ferramenta fornece sugestões das plataformas de destino mais adequadas, a preparação para a nuvem e um nível de correção de aplicativo/banco de dados para determinada carga de trabalho. Ela oferece um cálculo simples com um clique e a geração de relatórios que ajuda a acelerar avaliações de acervos grandes, fornecendo um processo de decisão uniforme e automatizado para plataforma de destino.|
|[Utilitário DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Use o DBLoader para carregar dados de arquivos de texto delimitados no SQL Server. Esse utilitário do console do Windows usa a interface de carregamento em massa do cliente nativo do SQL Server. A interface funciona em todas as versões do SQL Server, com o Banco de Dados SQL do Azure.|
|[Criação de banco de dados em massa com o PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Use um conjunto de três scripts do PowerShell que criam um grupo de recursos (create_rg.ps1), o [servidor lógico no Azure](../../database/logical-servers.md) (create_sqlserver.ps1) e um banco de dados SQL (create_sqldb.ps1). Os scripts incluem funcionalidades de loop, para que você possa iterar e criar quantos servidores e bancos de dados forem necessários.|
|[Implantação de esquema em massa com o MSSQL-Scripter e o PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Esse ativo cria um grupo de recursos, cria um ou vários [servidores lógicos no Azure](../../database/logical-servers.md) para hospedar o Banco de Dados SQL do Azure, exporta todos os esquemas de uma instância local do SQL Server (ou várias instâncias do SQL Server 2005 e posterior) e importa os esquemas para o Banco de Dados SQL do Azure.|
|[Converter trabalhos do SQL Server Agent em trabalhos de banco de dados elástico](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Esse script migra seus trabalhos de origem do SQL Server Agent para trabalhos de banco de dados elástico.|
|[Enviar emails do Banco de Dados SQL do Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Essa solução é uma alternativa à funcionalidade do SendMail e está disponível para SQL Server locais. Ela usa o Azure Functions e o serviço SendGrid para enviar emails do Banco de Dados SQL do Azure.|
|[Utilitário para mover os logons do SQL Server local para o Banco de Dados SQL do Azure](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Um script do PowerShell pode criar um script de comando T-SQL para recriar logons e selecionar usuários de banco de dados do SQL Server local para o Banco de Dados SQL do Azure. A ferramenta permite o mapeamento automático de contas do Windows Server Active Directory para contas do Azure AD, com a migração opcional de logons nativos do SQL Server.|
|[Automação da coleta de dados do Perfmon usando o Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Use a ferramenta Logman para coletar dados do Perfmon (para ajudar você a entender o desempenho da linha de base) e obter recomendações de destinos de migração. Essa ferramenta usa o logman.exe para criar o comando que vai criar, iniciar, parar e excluir os contadores de desempenho definidos em uma instância remota do SQL Server.|
|[Migração de banco de dados do Banco de Dados SQL do Azure usando o BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Este white paper fornece diretrizes e etapas para ajudar a acelerar as migrações do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC.|

A equipe de engenharia de dados do SQL desenvolveu esses recursos. A principal responsabilidade dessa equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Microsoft Azure.


## <a name="next-steps"></a>Próximas etapas

- Para começar a migrar seus bancos de dados do SQL Server para o Banco de Dados SQL do Azure, confira o [Guia de migração do SQL Server para o Banco de Dados SQL do Azure](sql-server-to-sql-database-guide.md).

- Para obter uma matriz de serviços e ferramentas que podem ajudar você com os cenários de migração de dados e banco de dados, bem como tarefas especializadas, confira [Serviços e ferramentas de migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Banco de Dados SQL, confira:
   - [Visão geral do Banco de Dados SQL do Azure](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, confira:
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Melhores práticas para estimar os custos e dimensionar as cargas de trabalho migradas para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, confira [Data Access Migration Toolkit (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Para obter detalhes de como executar testes A/B na camada de acesso a dados, confira [Assistente para Experimentos de Banco de Dados](/sql/dea/database-experimentation-assistant-overview).
