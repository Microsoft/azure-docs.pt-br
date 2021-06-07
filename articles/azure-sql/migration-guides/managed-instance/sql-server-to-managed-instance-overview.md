---
title: 'SQL Server para o SQL Instância Gerenciada do Azure: visão geral da migração'
description: Saiba mais sobre as diferentes ferramentas e opções disponíveis para migrar seus bancos de dados do SQL Server para o SQL Instância Gerenciada do Azure.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: a3876b63e9dc41a22ac6e95b31f34665a0d0bdd8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642342"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Visão geral da migração: SQL Server para SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Saiba mais sobre as diferentes opções de migração e considerações para migrar seus SQL Server para o SQL Instância Gerenciada do Azure. 

Você pode migrar SQL Server em execução no local ou em: 

- SQL Server em Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de banco de dados relacional do Amazon (AWS RDS) 
- Mecanismo de computação (Google Cloud Platform-GCP)  
- SQL de nuvem para SQL Server (Google Cloud Platform – GCP) 

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Visão geral

O [Azure SQL instância gerenciada](../../managed-instance/sql-managed-instance-paas-overview.md) é uma opção de destino recomendada para cargas de trabalho de SQL Server que exigem um serviço totalmente gerenciado sem precisar gerenciar máquinas virtuais ou seus sistemas operacionais. O SQL Instância Gerenciada permite que você tire e SHIFTe seus aplicativos locais para o Azure com o mínimo de alterações de aplicativo ou banco de dados, ao mesmo tempo em que tem total isolamento de suas instâncias com suporte de rede virtual nativa (VNet). 

## <a name="considerations"></a>Considerações 

Os principais fatores a serem considerados ao avaliar as opções de migração dependem: 
- Número de servidores e bancos de dados
- Tamanho dos bancos de dados
- Tempo de inatividade de negócios aceitável durante o processo de migração 

Um dos principais benefícios da migração de seus SQL Servers para o SQL Instância Gerenciada é que você pode optar por migrar toda a instância ou apenas um subconjunto de bancos de dados individuais. Planeje atentamente para incluir o seguinte em seu processo de migração: 
- Todos os bancos de dados que precisam ser colocados na mesma instância 
- Objetos de nível de instância necessários para seu aplicativo, incluindo logons, credenciais, trabalhos e operadores do SQL Agent e gatilhos de nível de servidor. 

> [!NOTE]
> O Azure SQL Instância Gerenciada garante 99,99% de disponibilidade mesmo em cenários críticos, portanto, a sobrecarga causada por alguns recursos no SQL MI não pode ser desabilitada. Para obter mais informações, consulte as [causas raiz que podem causar um desempenho diferente no SQL Server e no blog do SQL instância gerenciada do Azure](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) . 


## <a name="choose-appropriate-target"></a>Escolha o destino apropriado

Algumas diretrizes gerais para ajudá-lo a escolher a camada de serviço e as características corretas do SQL Instância Gerenciada para ajudar a corresponder a sua [linha de base de desempenho](sql-server-to-managed-instance-performance-baseline.md): 

- Use a linha de base uso da CPU para provisionar uma instância gerenciada que corresponda ao número de núcleos que sua instância do SQL Server usa. Pode ser necessário dimensionar os recursos para que correspondam às [características de geração de hardware](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Use a linha de base uso de memória para escolher uma [opção vCore](../../managed-instance/resource-limits.md#service-tier-characteristics) que corresponda apropriadamente à sua alocação de memória. 
- Use a latência de e/s de linha de base do subsistema de arquivos para escolher entre as camadas de serviço Uso Geral (latência maior que 5 ms) e Comercialmente Crítico (latência inferior a 3 ms). 
- Use a taxa de transferência de linha de base para prefixar o tamanho dos arquivos de dados e de log para atingir o desempenho esperado de e/s. 

Você pode escolher recursos de computação e armazenamento durante a implantação e, em seguida, [alterá-los depois de usar o portal do Azure](../../database/scale-resources.md) sem incorrer em tempo de inatividade para seu aplicativo. 

> [!IMPORTANT]
> Qualquer discrepância nos requisitos de [rede virtual de instância gerenciada](../../managed-instance/connectivity-architecture-overview.md#network-requirements) pode impedi-lo de criar novas instâncias ou usar aquelas existentes. Saiba mais sobre como [criar novas](../../managed-instance/virtual-network-subnet-create-arm-template.md)   e [configurar redes existentes](../../managed-instance/vnet-existing-add-subnet.md)   . 

Outra consideração importante na seleção da camada de serviço de destino no Azure SQL Instância Gerenciada (Uso Geral vs Comercialmente Crítico) é a disponibilidade de determinados recursos, como In-Memory OLTP que está disponível apenas na camada Comercialmente Crítico. 

### <a name="sql-server-vm-alternative"></a>Alternativa de VM SQL Server

Sua empresa pode ter requisitos que fazem SQL Server nas VMs do Azure um destino mais adequado do que o SQL Instância Gerenciada do Azure. 

Se o seguinte se aplicar ao seu negócio, considere mudar para uma VM SQL Server em vez disso: 

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
| [Migrações para Azure](../../../migrate/how-to-create-azure-sql-assessment.md) | As migrações para Azure para SQL do Azure permitem que você descubra e avalie seu espaço de dados SQL em escala quando estiver no VMware, fornecendo recomendações de implantação do Azure SQL, dimensionamento de destino e estimativas mensais. | 
|[DMS (Serviço de Migração de Banco de Dados do Azure)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Serviço do Azure de primeira empresa que dá suporte à migração no modo offline para aplicativos que podem proporcionar tempo de inatividade durante o processo de migração. Ao contrário da migração contínua no modo online, a migração de modo offline executa uma restauração única de um backup de banco de dados completo da origem para o destino. | 
|[Backup e restauração nativos](../../managed-instance/restore-sample-database-quickstart.md) | O SQL Instância Gerenciada dá suporte à restauração de backups de banco de dados nativos SQL Server (arquivos. bak), tornando-o a opção de migração mais fácil para clientes que podem fornecer backups de banco de dados completos para o armazenamento do Azure. Os backups completo e diferencial também têm suporte e estão documentados na [seção ativos de migração](#migration-assets) mais adiante neste artigo.| 
|[Serviço de reprodução de log (LRS)](../../managed-instance/log-replay-service-migrate.md) | Esse é um serviço de nuvem habilitado para Instância Gerenciada com base na tecnologia de envio de logs do SQL Server, tornando-o uma opção de migração para clientes que podem fornecer backups de banco de dados completos, diferenciais e de log para o armazenamento do Azure. LRS é usado para restaurar arquivos de backup do armazenamento de BLOBs do Azure para o SQL Instância Gerenciada.| 
| | |

### <a name="alternative-tools"></a>Ferramentas alternativas

A tabela a seguir lista as ferramentas de migração alternativas: 

|**Tecnologia** |**Descrição**  |
|---------|---------|
|[Replicação transacional](../../managed-instance/replication-transactional-overview.md) | Replique os dados das tabelas SQL Server de origem para o SQL Instância Gerenciada fornecendo uma opção de migração de tipo Publicador de assinante, mantendo a consistência transacional. | 
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| O [utilitário bcp (programa de cópia em massa)](/sql/tools/bcp-utility) copia dados de uma instância do SQL Server em um arquivo de dados. Use o utilitário BCP para exportar os dados de sua origem e importar o arquivo de dados para o SQL de destino Instância Gerenciada.</br></br> Para operações de cópia em massa de alta velocidade a fim de mover dados para o Azure SQL Database, a [ferramenta de cópia em massa inteligente](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) pode ser usada para maximizar as velocidades de transferência aproveitando as tarefas de cópia paralelas. | 
|[Assistente para importar/exportar/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) é um arquivo do Windows com uma `.bacpac` extensão que encapsula o esquema e os dados de um banco de dado. O BACPAC pode ser usado para exportar dados de um SQL Server de origem e para importar o arquivo de volta para o SQL Instância Gerenciada do Azure.  |  
|[ADF (Azure Data Factory)](../../../data-factory/connector-azure-sql-managed-instance.md)| A [atividade de cópia](../../../data-factory/copy-activity-overview.md) no Azure data Factory migra dados do banco de SQL Server de origem (s) para o SQL instância gerenciada usando conectores internos e um [Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> O ADF dá suporte a uma ampla gama de [conectores](../../../data-factory/connector-overview.md) para mover dados de fontes de SQL Server para o SQL instância gerenciada. |
| | |

## <a name="compare-migration-options"></a>Comparar opções de migração

Compare as opções de migração para escolher o caminho apropriado às suas necessidades de negócios. 

### <a name="recommended-options"></a>Opções recomendadas

A tabela a seguir compara as opções de migração recomendadas: 

|Opção de migração  |Quando usar  |Considerações  |
|---------|---------|---------|
|[DMS (Serviço de Migração de Banco de Dados do Azure)](../../../dms/tutorial-sql-server-to-managed-instance.md) | -Migre bancos de dados individuais ou vários bancos de dados em escala. </br> -Pode acomodar o tempo de inatividade durante o processo de migração. </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM |  -As migrações em escala podem ser automatizadas por meio do [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> -Tempo para concluir a migração depende do tamanho do banco de dados e afetado pelo tempo de backup e restauração. </br> -Pode ser necessário um tempo de inatividade suficiente. |
|[Backup e restauração nativos](../../managed-instance/restore-sample-database-quickstart.md) | -Migrar bancos de dados de aplicativo de linha de negócios individuais.  </br> -Migração rápida e fácil sem um serviço de migração ou uma ferramenta separada.  </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM | -O backup do banco de dados usa vários threads para otimizar a transferência de dados para o armazenamento de BLOBs do Azure, mas a largura de banda e o tamanho do banco de </br> -A inatividade deve acomodar o tempo necessário para executar um backup completo e restauração (que é um tamanho de operação de dados).| 
|[Serviço de reprodução de log (LRS)](../../managed-instance/log-replay-service-migrate.md) | -Migrar bancos de dados de aplicativo de linha de negócios individuais.  </br> -Mais controle é necessário para migrações de banco de dados.  </br> </br> Fontes com suporte: </br> -SQL Server (2008-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM | -A migração envolve fazer backups de banco de dados completos em SQL Server e copiar arquivos de backup para o armazenamento de BLOBs do Azure. LRS é usado para restaurar arquivos de backup do armazenamento de BLOBs do Azure para o SQL Instância Gerenciada. </br> -Os bancos de dados que estão sendo restaurados durante o processo de migração estarão em um modo de restauração e não poderão ser usados para leitura ou gravação até que o processo seja concluído.| 
| | | |

### <a name="alternative-options"></a>Opções alternativas

A tabela a seguir compara as opções de migração alternativas: 

|Método/tecnologia |Quando usar |Considerações  |
|---------|---------|---------|
|[Replicação transacional](../../managed-instance/replication-transactional-overview.md) | – Migre continuamente publicando alterações de tabelas de banco de dados de origem para tabelas de banco de dados SQL Instância Gerenciada de destino. </br> -Migrações de banco de dados completas ou parciais de tabelas selecionadas (subconjunto de banco de dados).  </br> </br> Fontes com suporte: </br> -SQL Server (2012-2019) com algumas limitações </br> -AWS EC2  </br> -GCP computação SQL Server VM | </br> -A instalação é relativamente complexa em comparação com outras opções de migração.   </br> -Fornece uma opção de replicação contínua para migrar dados (sem colocar os bancos de dados offline).</br> -A replicação transacional tem várias limitações a serem consideradas ao configurar o Publicador no SQL Server de origem. Confira [limitações na publicação de objetos](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) para saber mais.  </br> -A capacidade de [monitorar a atividade de replicação](/sql/relational-databases/replication/monitor/monitoring-replication) está disponível.    |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Migrando migrações de dados completas ou parciais. </br> -Pode acomodar o tempo de inatividade. </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM   | -Requer tempo de inatividade para exportar dados da origem e importar para o destino. </br> -Os formatos de arquivo e os tipos de dados usados na exportação/importação precisam ser consistentes com os esquemas de tabela. |
|[Assistente para importar/exportar/BACPAC](../../database/database-import.md)| -Migrar bancos de dados de aplicativo de linha de negócios individuais. </br>-Adequado para bancos de dados menores.  </br>  Não requer uma ferramenta ou serviço de migração separado. </br> </br> Fontes com suporte: </br> -SQL Server (2005-2019) local ou VM do Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP computação SQL Server VM  |   </br> -Requer tempo de inatividade, pois os dados precisam ser exportados na origem e importados no destino.   </br> -Os formatos de arquivo e os tipos de dados usados na exportação/importação precisam ser consistentes com os esquemas de tabela para evitar erros de incompatibilidade de tipo de dados/truncamento. </br> -Tempo necessário para exportar um banco de dados com um grande número de objetos pode ser significativamente maior. |
|[ADF (Azure Data Factory)](../../../data-factory/connector-azure-sql-managed-instance.md)| -Migrando e/ou transformando dados de origem SQL Server banco (s).</br> -Mesclando dados de várias fontes de dados para o Azure SQL Instância Gerenciada normalmente para cargas de trabalho de BI (Business Intelligence).   </br> -Requer a criação de pipelines de movimentação de dados no ADF para mover dados da origem para o destino.   </br> - O [custo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) é uma consideração importante e é baseado nos gatilhos de pipeline, execuções de atividade, duração da movimentação de dados, etc. |
| | | |

## <a name="feature-interoperability"></a>Interoperabilidade de recursos 

Há considerações adicionais ao migrar cargas de trabalho que dependem de outros recursos de SQL Server. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migre pacotes do SQL Server Integration Services (SSIS) e projetos no SSISDB para o SQL Instância Gerenciada do Azure usando o [serviço de migração de banco de dados do Azure (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Somente pacotes SSIS no SSISDB a partir do SQL Server 2012 têm suporte para migração. Converta pacotes SSIS herdados antes da migração. Consulte o [tutorial de conversão do projeto](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) para saber mais. 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Os relatórios do SQL Server Reporting Services (SSRS) podem ser migrados para relatórios paginados no Power BI. Use a [ferramenta de migração RDL](https://github.com/microsoft/RdlMigration) para ajudar a preparar e migrar seus relatórios. Essa ferramenta foi desenvolvida pela Microsoft para ajudar os clientes a migrar relatórios RDL de seus servidores do SSRS para o Power BI. Ele está disponível no GitHub e documenta uma explicação completa do cenário de migração. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services modelos de tabela do SQL Server 2012 e superior podem ser migrados para Azure Analysis Services, que é um modelo de implantação PaaS para Analysis Services modelo de tabela no Azure. Você pode aprender mais sobre a migração de modelos locais para Azure Analysis Services neste [tutorial de vídeo](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Como alternativa, você também pode considerar a migração de seus modelos de tabela Analysis Services locais para [Power bi Premium usando os novos pontos de extremidade de leitura/gravação XMLA](/power-bi/admin/service-premium-connect-tools). 
> [!NOTE]
> Power BI a funcionalidade de pontos de extremidade de leitura/gravação XMLA está atualmente em visualização pública e não deve ser considerada para cargas de trabalho de produção até que a funcionalidade fique disponível para o público geral.

#### <a name="high-availability"></a>Alta disponibilidade

Os recursos de alta disponibilidade do SQL Server Always On instâncias de cluster de failover e grupos de disponibilidade Always On se tornam obsoletos na Instância Gerenciada de destino do SQL do Azure como a arquitetura de alta disponibilidade já está incorporada em ambos os uso geral [(modelo de disponibilidade padrão)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e comercialmente crítico [do SQL (modelo de disponibilidade Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) . O modelo de disponibilidade Premium também fornece expansão de leitura que permite a conexão em um dos nós secundários para fins somente leitura.     

Além da arquitetura de alta disponibilidade incluída no SQL Instância Gerenciada, também há o recurso [grupos de failover automático](../../database/auto-failover-group-overview.md) que permite gerenciar a replicação e o failover de bancos de dados em uma instância gerenciada para outra região. 

#### <a name="sql-agent-jobs"></a>Trabalhos do SQL Agent

Use a opção offline do serviço de migração de banco de dados do Azure (DMS) para migrar [trabalhos do SQL Agent](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). Caso contrário, gerar script de trabalhos no Transact-SQL (T-SQL) usando SQL Server Management Studio e recriá-los manualmente no Instância Gerenciada SQL de destino. 

> [!IMPORTANT]
> Atualmente, o Azure DMS só dá suporte a trabalhos com etapas de subsistema T-SQL. Trabalhos com etapas do pacote SSIS precisarão ser migrados manualmente. 

#### <a name="logins-and-groups"></a>Logons e grupos

Os logons do SQL do SQL Server de origem podem ser movidos para o Azure SQL Instância Gerenciada usando o serviço de migração de banco de dados (DMS) no modo offline.  Use a folha **[selecionar logons](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** no **Assistente de migração** para migrar logons para o SQL de destino instância gerenciada. 

Por padrão, o Serviço de Migração de Banco de Dados do Azure é compatível apenas com a migração de logons SQL. No entanto, você pode habilitar a capacidade de migrar logons do Windows executando as seguintes ações:

Garantir que o SQL de destino Instância Gerenciada tenha acesso de leitura do Azure AD, que pode ser configurado por meio do portal do Azure por um usuário com a função de **administrador global** .
Configurando sua instância do Serviço de Migração de Banco de Dados do Azure para habilitar as migrações de logon de usuário/grupo do Windows, que são configuradas por meio do portal do Azure, na página de Configuração. Depois de habilitar essa configuração, reinicie o serviço para que as alterações entrem em vigor.

Depois de reiniciar o serviço, os logons de usuário/grupo do Windows aparecerão na lista de logons disponíveis para migração. Para todo logon de usuário/grupo do Windows que você migrar, será solicitado que forneça o nome de domínio associado. As contas de usuário de serviço (conta com nome de domínio NT AUTHORITY) e as contas de usuário virtual (nome da conta com o nome de domínio NT SERVICE) não são compatíveis.

Para saber mais, confira [como migrar usuários e grupos do Windows em uma instância de SQL Server para o SQL do Azure instância gerenciada usando o T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Como alternativa, você pode usar a [ferramenta do utilitário do PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) especialmente projetada pelos arquitetos de migração de dados da Microsoft. O utilitário usa o PowerShell para criar um script T-SQL para recriar logons e Selecionar usuários de banco de dados da origem para o destino. A ferramenta mapeia automaticamente as contas do Windows AD para as contas do Azure AD e pode fazer uma pesquisa de UPN para cada logon no Active Directory de origem. A ferramenta scripts de servidor personalizado e funções de banco de dados, bem como associação de função, função de banco de dados e permissões de usuário. Os bancos de dados independentes não têm suporte no momento e apenas um subconjunto de possíveis SQL Server permissões são incluídos no script. 

#### <a name="encryption"></a>Criptografia

Ao migrar bancos de dados protegidos por  [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)   para uma instância gerenciada usando a opção de restauração nativa, [migre o certificado correspondente](../../managed-instance/tde-certificate-migrate.md) do SQL Server de origem para o instância gerenciada de destino SQL *antes* da restauração do banco de dados. 

#### <a name="system-databases"></a>Bancos de dados do sistema

Não há suporte para restauração de bancos de dados do sistema. Para migrar objetos de nível de instância (armazenados em bancos de dados mestre ou msdb), crie scripts usando Transact-SQL (T-SQL) e, em seguida, recriá-los na instância gerenciada de destino. 

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabelas com otimização de memória)

O SQL Server fornece In-Memory recurso OLTP que permite o uso de tabelas com otimização de memória, tipos de tabela com otimização de memória e módulos do SQL compilados nativamente para executar cargas de trabalho que têm requisitos de processamento transacional de alta taxa de transferência e baixa latência. 

> [!IMPORTANT]
> In-Memory OLTP só tem suporte na camada de Comercialmente Crítico no Azure SQL Instância Gerenciada (e sem suporte na camada Uso Geral).

Se você tiver tabelas com otimização de memória ou tipos de tabela com otimização de memória em sua SQL Server local e estiver procurando migrar para o SQL do Azure Instância Gerenciada, você deve:

- Escolha Comercialmente Crítico camada para o Instância Gerenciada de destino do Azure SQL que dá suporte ao OLTP In-Memory ou
- Se você quiser migrar para Uso Geral camada no Instância Gerenciada SQL do Azure, remova tabelas com otimização de memória, tipos de tabela com otimização de memória e módulos SQL compilados nativamente que interagem com objetos com otimização de memória antes de migrar seus bancos de dados. A seguinte consulta T-SQL pode ser usada para identificar todos os objetos que precisam ser removidos antes da migração para Uso Geral camada:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

Para saber mais sobre as tecnologias na memória, consulte [otimizar o desempenho usando tecnologias na memória no banco de dados SQL do Azure e Azure sql instância gerenciada](../../in-memory-oltp-overview.md)

## <a name="leverage-advanced-features"></a>Aproveite os recursos avançados 

Não se esqueça de aproveitar os recursos avançados baseados em nuvem oferecidos pelo SQL Instância Gerenciada. Por exemplo, você não precisa mais se preocupar com o gerenciamento de backups, pois o serviço faz isso para você. Você pode restaurar para qualquer [ponto no tempo dentro do período de retenção](../../database/recovery-using-backups.md#point-in-time-restore). Além disso, você não precisa se preocupar com a configuração de alta disponibilidade, pois a [alta disponibilidade é interna](../../database/high-availability-sla.md). 

Para reforçar a segurança, considere o uso de [Azure Active Directory autenticação](../../database/authentication-aad-overview.md), [auditoria](../../managed-instance/auditing-configure.md), [detecção de ameaças](../../database/azure-defender-for-sql.md), [segurança em nível de linha](/sql/relational-databases/security/row-level-security)e máscara de [dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking).

Além dos recursos avançados de gerenciamento e segurança, o SQL Instância Gerenciada fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorar e ajustar sua carga de trabalho](../../database/monitor-tune-overview.md). [Análise de SQL do Azure](../../../azure-monitor/insights/azure-sql.md) permite monitorar um grande conjunto de instâncias gerenciadas de maneira centralizada.  [Ajuste automático](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   em instâncias gerenciadas, o monitora continuamente o desempenho das estatísticas de execução do plano SQL e corrige automaticamente os problemas de desempenho identificados. 

Alguns recursos só estarão disponíveis quando o [nível de compatibilidade do banco de dados](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) for alterado para o nível de compatibilidade mais recente (150). 

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional, consulte os recursos a seguir que foram desenvolvidos para projetos de migração do mundo real.

|Ativo  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado.|
|[Utilitário DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|O DBLoader pode ser usado para carregar dados de arquivos de texto delimitados em SQL Server. Este utilitário de console do Windows usa a interface SQL Server Native Client carregamento em massa, que funciona em todas as versões do SQL Server, incluindo o SQL do Azure MI.|
|[Utilitário para mover os logons locais SQL Server para o SQL do Azure Instância Gerenciada](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Um script do PowerShell que cria um script de comando T-SQL para recriar logons e Selecionar usuários de banco de dados do SQL Server local para o SQL Instância Gerenciada do Azure. A ferramenta permite o mapeamento automático de contas do Windows AD para contas do Azure AD, bem como a migração opcional de SQL Server logons nativos.|
|[Automação de coleta de dados de Perfmon usando logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Uma ferramenta que coleta dados de execução para entender o desempenho de linha de base que auxilia na recomendação de destino de migração. Essa ferramenta que usa logman.exe para criar o comando que irá criar, iniciar, parar e excluir contadores de desempenho definidos em um SQL Server remoto.|
|[White Paper-migração de banco de dados para o Azure SQL Instância Gerenciada Restaurando backups completos e diferenciais](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Este white paper fornece orientações e etapas para ajudar a acelerar as migrações de SQL Server para o SQL do Azure Instância Gerenciada se você tiver apenas backups totais e diferenciais (e nenhum recurso de backup de log).|

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.


## <a name="next-steps"></a>Próximas etapas

Para começar a migrar seus SQL Server para o SQL Instância Gerenciada do Azure, consulte o [SQL Server guia de migração de instância gerenciada do SQL do Azure](sql-server-to-managed-instance-guide.md).

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Azure SQL Instância Gerenciada consulte:
   - [Camadas de serviço no Azure SQL Instância Gerenciada](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferenças entre SQL Server e SQL Instância Gerenciada do Azure](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).