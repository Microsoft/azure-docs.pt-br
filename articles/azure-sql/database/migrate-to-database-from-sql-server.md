---
title: Migração do banco de dados do SQL Server para o Banco de Dados SQL do Azure
description: Saiba mais sobre a migração SQL Server banco de dados para o banco de dados SQL do Azure.
keywords: migração de banco de dados, migração de banco de dados do sql server, ferramentas de migração de banco de dados, migrar banco de dados, migrar banco de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: fab52fcea03a2f65c868cfac27f8a8cef115b2be
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917640"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migração do banco de dados do SQL Server para o Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste artigo, você aprende sobre os principais métodos para migrar um banco de dados SQL Server 2005 ou posterior para o banco de dados SQL do Azure. Para obter informações sobre como migrar para o Azure SQL Instância Gerenciada, consulte [migrar uma instância de SQL Server para instância gerenciada do SQL do Azure](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md). Para obter orientação sobre como escolher opções de migração e ferramentas para migrar para o SQL do Azure, consulte [migrar para o SQL do Azure](../migration-guides/index.yml)


## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrar para um banco de dados individual ou um banco de dados em pool

Há dois métodos principais para migrar um banco de dados SQL Server 2005 ou posterior para o banco de dados SQL do Azure. O primeiro método é mais simples, mas requer algum tempo de inatividade, possivelmente substancial, durante a migração. O segundo método é mais complexo, mas elimina substancialmente o tempo de inatividade durante a migração.

Em ambos os casos, você precisa garantir que o banco de dados de origem seja compatível com o Banco de Dados SQL do Azure usando o [DMA (Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595). O banco de dados SQL está se aproximando da [paridade de recursos](features-comparison.md) com SQL Server, além de problemas relacionados a operações de nível de servidor e de banco de dados. Os bancos de dados e os aplicativos que dependem de [funções com suporte parcial ou sem suporte](transact-sql-tsql-differences-sql-server.md) precisarão de alguma [reengenharia que corrija essas incompatibilidades](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) para que o banco de dados SQL Server possa ser migrado.

> [!NOTE]
> Para migrar um banco de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para o Banco de Dados SQL do Azure, confira [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Método 1: migração com tempo de inatividade durante a migração

 Use esse método para migrar para um banco de dados único ou em pool se você puder pagar algum tempo de inatividade ou se estiver executando uma migração de teste de um banco de dados de produção para migração posterior. Para ver um tutorial, consulte [Migrar um Banco de Dados do SQL Server](../../dms/tutorial-sql-server-to-azure-sql.md).

A lista a seguir contém o fluxo de trabalho geral para uma migração de banco de dados do SQL Server de um banco de dados individual ou em pool usando esse método. Para migração para o SQL Instância Gerenciada, consulte [migração para o sql instância gerenciada](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![Diagrama de migração do VSSSDT](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Avalie](/sql/dma/dma-assesssqlonprem) o banco de dados em termos de compatibilidade usando a versão mais recente do [DMA (Assistente de Migração de Dados)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Prepare as correções necessárias como scripts Transact-SQL.
3. Faça uma cópia transacionalmente consistente do banco de dados de origem que está sendo migrado ou interrompa novas transações no banco de dados de origem durante a migração. Os métodos para realizar essa última opção incluem a desabilitação da conectividade de cliente ou a criação de um [instantâneo do banco de dados](/sql/relational-databases/databases/create-a-database-snapshot-transact-sql). Após a migração, você poderá usar a replicação transacional para atualizar os bancos de dados migrados com alterações ocorridas após o ponto de corte para a migração. Consulte [Migrar usando a migração transacional](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Implante os scripts Transact-SQL para aplicar as correções à cópia do banco de dados.
5. [Migre](/sql/dma/dma-migrateonpremsql) a cópia do banco de dados para um novo banco de dados no banco de dados SQL do Azure usando o assistente de migração de dados.

> [!NOTE]
> Em vez de usar o DMA, também use um arquivo BACPAC. Consulte [importar um arquivo BACPAC para um novo banco de dados no banco de dados SQL do Azure](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Otimizando o desempenho de transferência de dados durante a migração

A lista a seguir contém recomendações para melhorar o desempenho durante o processo de importação.

- Escolha a camada de serviço e tamanho da computação mais altos que seu orçamento permitir para maximizar o desempenho de transferência. Você pode reduzir verticalmente após a migração para economizar dinheiro.
- Minimize a distância entre o arquivo BACPAC e o data center de destino.
- Desabilitar autostatistics durante a migração
- Índices e tabelas de partição
- Descartar exibições indexadas e recriá-las após a conclusão
- Remova os dados históricos raramente consultados para outro banco e migre esses dados históricos para um banco de dado separado no banco de dados SQL do Azure. Em seguida, você pode consultar esses dados históricos usando [consultas elásticas](elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Otimizar o desempenho após a migração ser concluída

[Atualize as estatísticas](/sql/t-sql/statements/update-statistics-transact-sql) com uma verificação completa após a migração ser concluída.

## <a name="method-2-use-transactional-replication"></a>Método 2: usar replicação transacional

Quando você não puder remover seu banco de dados SQL Server da produção enquanto a migração estiver ocorrendo, você poderá usar SQL Server replicação transacional como sua solução de migração. Para usar esse método, o banco de dados de origem deve atender a [requisitos para replicação transacional](./replication-to-sql-database.md) e ser compatível com o banco de dados SQL do Azure. Para saber mais sobre a replicação do SQL com o AlwaysOn, consulte [Configurar a replicação para Grupos de Disponibilidade AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Para usar essa solução, configure seu banco de dados no banco de dados SQL do Azure como um assinante para a instância de SQL Server que você deseja migrar. O distribuidor de replicação transacional sincroniza os dados do banco de dados a ser sincronizado (o editor), enquanto as novas transações continuam a ocorrer.

Com a replicação transacional, todas as alterações em seus dados ou esquema aparecem em seu banco de dado no banco de dados SQL do Azure. Quando a sincronização estiver concluída e você estiver pronto para migrar, altere a cadeia de conexão de seus aplicativos para apontar para o banco de dados. Assim que a replicação transacional realizar todas as alterações restantes no banco de dados e todos os aplicativos apontarem para o Banco de Dados do Azure, você poderá desinstalar a replicação transacional. Seu banco de dados no banco de dados SQL do Azure agora é seu sistema de produção.

 ![Diagrama do SeedCloudTR](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> Você também pode usar a replicação transacional para migrar um subconjunto do banco de dados de origem. A publicação que você replica no Banco de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas no banco de dados que está sendo replicado. Para cada tabela sendo replicada, você poderá limitar os dados a um subconjunto de linhas e/ou um subconjunto de colunas.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migração para o Banco de Dados SQL usando o fluxo de trabalho da Replicação de Transação

> [!IMPORTANT]
> Use a versão mais recente do SQL Server Management Studio para permanecer sincronizada com as atualizações do Azure e do banco de dados SQL. Versões anteriores do SQL Server Management Studio não podem configurar o Banco de Dados SQL como um assinante. [Atualizar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Configurar a distribuição
   - [Usando o SSMS (SQL Server Management Studio)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Usando Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Criar a publicação
   - [Usando o SSMS (SQL Server Management Studio)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Usando Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Criar Assinatura
   - [Usando o SSMS (SQL Server Management Studio)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Usando Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

Algumas dicas e diferenças da migração para o Banco de Dados SQL

- Usar um distribuidor local
  - Fazer isso causa um impacto de desempenho no servidor.
  - Se o impacto de desempenho for inaceitável, você poderá usar outro servidor, mas isso adicionará complexidade ao gerenciamento e à administração.
- Ao selecionar uma pasta de instantâneo, verifique se a pasta selecionada é grande o suficiente para manter um BCP de cada tabela que você deseja replicar.
- A criação de instantâneos bloqueia as tabelas associadas até que ela seja concluída, portanto, agende seu instantâneo adequadamente.
- Há suporte apenas para assinaturas push no Banco de Dados SQL do Azure. Você só pode adicionar assinantes do banco de dados de origem.

## <a name="resolving-database-migration-compatibility-issues"></a>Resolvendo problemas de compatibilidade de migração do banco de dados

Há uma ampla variedade de problemas de compatibilidade que podem ser encontrados, dependendo da versão do SQL Server no banco de dados de origem e da complexidade do banco de dados que você está migrando. Versões anteriores do SQL Server têm mais problemas de compatibilidade. Use os recursos a seguir, além de uma pesquisa direcionada na Internet, usando o mecanismo de pesquisa de sua preferência:

- [Recursos de banco de dados do SQL Server sem suporte no Banco de Dados SQL do Azure](transact-sql-tsql-differences-sql-server.md)
- [Funcionalidade do Mecanismo de Banco de Dados descontinuada no SQL Server 2016](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server)
- [Funcionalidade do Mecanismo de Banco de Dados descontinuada no SQL Server 2014](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?viewFallbackFrom=sql-server-2014)
- [Funcionalidade de Mecanismo de Banco de Dados descontinuada no SQL Server 2012](/previous-versions/sql/sql-server-2012/ms144262(v=sql.110))
- [Discontinued Database Engine Functionality in SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms144262(v=sql.105))
- [Discontinued Database Engine Functionality in SQL Server 2005](/previous-versions/sql/sql-server-2005/ms144262(v=sql.90))

Além de Pesquisar a Internet e usar esses recursos, use a [página do Microsoft Q&uma pergunta para o banco de dados SQL do Azure ou o](/answers/topics/azure-sql-database.html) [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> O Azure SQL Instância Gerenciada permite migrar uma instância existente do SQL Server e seus bancos de dados com problemas mínimos ou sem compatibilidade. Consulte [o que é uma instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Use o script no blog dos Engenheiros EMEA do Azure SQL para [Monitorar o uso de tempdb durante migração](/archive/blogs/azuresqlemea/lesson-learned-10-monitoring-tempdb-usage).
- Use o script no blog dos Engenheiros EMEA do Azure SQL para [Monitorar o espaço de log de transações do banco de dados enquanto a migração está ocorrendo](/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).
- Para obter informações sobre como trabalhar com a hora UTC após a migração, confira [Modificando o fuso horário padrão para o fuso horário local](/archive/blogs/azuresqlemea/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone).
- Para obter informações sobre como alterar o idioma padrão de um banco de dados após a migração, confira [Como alterar o idioma padrão do Banco de Dados SQL do Azure](/archive/blogs/azuresqlemea/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database).
