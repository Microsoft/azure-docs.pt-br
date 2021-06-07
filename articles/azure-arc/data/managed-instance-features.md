---
title: Recursos e funcionalidades do SQL Instância Gerenciada habilitado para Arc do Azure
description: Recursos e funcionalidades do SQL Instância Gerenciada habilitado para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589212"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Recursos e funcionalidades do SQL Instância Gerenciada habilitado para Arc do Azure

O SQL Instância Gerenciada habilitado para arco do Azure compartilham uma base de código comum com a versão estável mais recente do SQL Server. A maior parte da linguagem SQL padrão, o processamento de consulta e os recursos de gerenciamento de banco de dados são idênticos. Os recursos que são comuns entre SQL Server e o banco de dados SQL ou SQL Instância Gerenciada são:

- Recursos de linguagem [– controle de palavras-chave de linguagem de fluxo](/sql/t-sql/language-elements/control-of-flow), [cursores](/sql/t-sql/language-elements/cursors-transact-sql), [tipos de dados](/sql/t-sql/data-types/data-types-transact-sql), [instruções DML](/sql/t-sql/queries/queries), [predicados](/sql/t-sql/queries/predicates), [números de sequência](/sql/relational-databases/sequence-numbers/sequence-numbers), [procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)e [variáveis](/sql/t-sql/language-elements/variables-transact-sql).
- Recursos de banco de dados – [ajuste automático (imposição de plano)](/sql/relational-databases/automatic-tuning/automatic-tuning), [controle de alterações](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [agrupamento de banco](/sql/relational-databases/collations/set-or-change-the-database-collation)de dados, bancos de dados [independentes](/sql/relational-databases/databases/contained-databases), [usuários independentes](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [compactação de data](/sql/relational-databases/data-compression/data-compression), definições de configuração de [banco](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)de dado, [operações de índice online](/sql/relational-databases/indexes/perform-index-operations-online), [particionamento](/sql/relational-databases/partitions/partitioned-tables-and-indexes)e [tabelas temporais](/sql/relational-databases/tables/temporal-tables) ([consulte o guia de introdução](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Recursos de segurança [– funções de aplicativo](/sql/relational-databases/security/authentication-access/application-roles), máscara de [dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) ([introdução à máscara de dados dinâmicos do banco de dados SQL com o portal do Azure](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [segurança em nível de linha](/sql/relational-databases/security/row-level-security)
- Funcionalidades de vários modelos – [processamento de grafo](/sql/relational-databases/graphs/sql-graph-overview), [dados JSON](/sql/relational-databases/json/json-data-sql-server), [OPENXML](/sql/t-sql/functions/openxml-transact-sql), [Spatial](/sql/relational-databases/spatial/spatial-data-sql-server), [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)e [índices XML](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Recursos do SQL Instância Gerenciada habilitado para o Arc do Azure

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS High Availability  
  
|Recurso|Instância Gerenciada de SQL habilitada para Azure Arc|
|-------------|----------------|
|Envio de logs|Sim| 
|Compactação de backup|Sim|
|Instantâneo do banco de dados|Sim|
|Instância do cluster de failover Always On<sup>1</sup>| Não aplicável. Recursos semelhantes disponíveis |
|Grupos de disponibilidade Always On<sup>2</sup>|Os recursos de HA são planejados.|
|Grupos de disponibilidade básicos <sup>2</sup>|Os recursos de HA são planejados.|
|Grupo de disponibilidade de confirmação de réplica mínimo <sup>2</sup>|Os recursos de HA são planejados.|
|Grupo de disponibilidade sem cluster|Sim|
|Restauração de arquivo e página online|Sim|
|Indexação online|Sim|
|Recompilações de índice online retomáveis|Sim|
|Alteração de esquema online|Sim|
|Recuperação rápida|Sim|
|Backups espelhados|Sim|
|Adição de memória a quente e CPU|Sim|
|Backup criptografado|Sim|
|Backup híbrido para o Azure (backup para URL)|Sim|

<sup>1</sup> no cenário em que há uma falha de Pod, um novo SQL instância gerenciada será iniciado e anexado novamente ao volume persistente que contém os dados. [Saiba mais sobre volumes persistentes kubernetes aqui](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> versões futuras fornecerão recursos do AG 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS Scalability and Performance  

| Recurso | Instância Gerenciada de SQL habilitada para Azure Arc |
|--|--|
| columnstore | Sim |
| Binários de objeto grandes em índices columnstore clusterizados | Sim |
| Recompilação de índice columnstore não clusterizado online | Sim |
| OLTP in-memory | Sim |
| Memória principal persistente | Sim |
| Particionamento de tabela e índice | Sim |
| Compactação de dados | Sim |
| Administrador de Recursos | Sim |
| Paralelismo de tabela particionada | Sim |
| Memória de página grande com reconhecimento para NUMA e alocação de matriz de buffer | Sim |
| Administração do recurso de E/S | Sim |
| Durabilidade atrasada | Sim |
| Ajuste Automático | Sim |
| Junções Adaptáveis de Modo de Lote | Sim |
| Comentários de Concessão de Memória do Modo de Lote | Sim |
| Execução Intercalada para Funções com Valor de Tabela de Várias Instruções | Sim |
| Aprimoramentos de inserção em massa | Sim |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS Security

| Recurso | Instância Gerenciada de SQL habilitada para Azure Arc |
|--|--|
| Segurança em nível de linha | Sim |
| Always Encrypted | Sim |
| Always Encrypted com enclaves seguros | Não |
| Mascaramento de dados dinâmicos | Sim |
| Auditoria básica | Sim |
| Auditoria refinada | Sim |
| Criptografia transparente do banco de dados | Sim |
| Funções definidas pelo usuário | Sim |
| Bancos de dados independentes | Sim |
| Criptografia para backups | Sim |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS Manageability  

| Recurso | Instância Gerenciada de SQL habilitada para Azure Arc |
|--|--|
| Conexão dedicada de administrador | Sim |
| Suporte de scripts PowerShell | Sim |
| Suporte para operações de componente do aplicativo da camada de dados – extrair, implantar, atualizar, excluir | Sim |
| Automação de política (verificação de agenda e alterações) | Sim |
| Coletor de dados de desempenho | Sim |
| Relatórios de desempenho padrão | Sim |
| Guias de plano e planejar congelamento para guias de plano | Sim |
| Direcione a consulta de exibições indexadas (usando a dica de NOEXPAND) | Sim |
| Manutenção automática de exibições indexadas | Sim |
| Exibições particionadas distribuídas | Sim |
| Operações indexadas paralelas | Sim |
| Uso automático da exibição indexada através do otimizador de consulta | Sim |
| Verificação de consistência paralela | Sim |

### <a name="programmability"></a><a name="Programmability"></a> Programmability  

| Recurso | Instância Gerenciada de SQL habilitada para Azure Arc |
|--|--|
| JSON | Sim |
| Repositório de Consultas | Sim |  |
| Temporal | Sim |  |
| Suporte a XML nativo | Sim |  |
| Indexação XML | Sim |  |
| Funcionalidades MERGE e UPSERT | Sim |  |
| Tipos de dados de Data e Hora | Sim |  |
| Suporte à internacionalização | Sim |  |
| Pesquisa semântica e de texto completo | Não |
| Especificação de idioma em consulta | Sim |  |
| Service Broker (mensagens) | Sim |  |
| pontos de extremidade Transact-SQL | Sim |  |
| Grafo | Sim |  |
| Serviços de Machine Learning | Não |  |
| PolyBase | Não |


### <a name="tools"></a>Ferramentas

O SQL Instância Gerenciada habilitado para Arc do Azure oferece suporte a várias ferramentas de dados que podem ajudá-lo a gerenciar seus dados.

| **Ferramenta** | Instância Gerenciada de SQL habilitada para Azure Arc|
| --- | --- | --- |
| Portal do Azure <sup>1</sup> | Não |
| CLI do Azure | Não |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Sim |
| Azure PowerShell | Sim |
| [Arquivo BACPAC (exportação)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sim |
| [Arquivo BACPAC (importação)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sim |
| [SSDT (SQL Server Data Tools)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Sim |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Sim |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Sim |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Sim |

<sup>1</sup> a Portal do Azure é usada somente para exibir instâncias gerenciadas do SQL do Azure Arc habilitadas no modo somente leitura durante a visualização.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Recursos sem suporte & serviços

Os seguintes recursos e serviços não estão disponíveis para o SQL Instância Gerenciada habilitado para Arc do Azure. O suporte para esses recursos será habilitado gradativamente com o passar do tempo.

| Área | Recurso ou serviço sem suporte |
|-----|-----|
| **Mecanismo de banco de dados** | Replicação de mesclagem |
| &nbsp; | Stretch DB |
| &nbsp; | Consulta distribuída com conexões de terceiros |
| &nbsp; | Servidores vinculados a fontes de dados diferentes de SQL Server e produtos SQL do Azure |
| &nbsp; | Procedimentos armazenados estendidos do sistema (XP_CMDSHELL etc.) |
| &nbsp; | Filetable, FILESTREAM |
| &nbsp; | Assemblies de CLR com definição de permissão EXTERNAL_ACCESS ou UNSAFE |
| &nbsp; | Buffer Pool Extension |
| **SQL Server Agent** |  Subsistemas: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Alertas |
| &nbsp; | Backup Gerenciado |
| **Alta disponibilidade** | Espelhamento de banco de dados  |
| **Segurança** | Gerenciamento Extensível de Chaves |
| &nbsp; | Autenticação do AD para servidores vinculados | 
| &nbsp; | Autenticação do AD para AGs (grupos de disponibilidade) | 