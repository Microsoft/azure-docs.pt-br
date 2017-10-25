---
title: "Migração do banco de dados do SQL Server para o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a migração de banco de dados do SQL Server para o Banco de Dados SQL do Azure na nuvem. Use ferramentas de migração do banco de dados para testar a compatibilidade antes da migração do banco de dados."
keywords: "migração de banco de dados, migração de banco de dados do sql server, ferramentas de migração de banco de dados, migrar banco de dados, migrar banco de dados sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
ms.openlocfilehash: 90c78007368c2679e1c5afdb9369869adde77f0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migração de banco de dados do SQL Server para o Banco de Dados SQL na nuvem
Neste artigo, você aprenderá sobre os dois principais métodos para migrar um banco de dados SQL Server 2005 ou posterior para o Banco de Dados SQL. O primeiro método é mais simples, mas requer algum tempo de inatividade, possivelmente substancial, durante a migração. O segundo método é mais complexo, mas elimina substancialmente o tempo de inatividade durante a migração.

Em ambos os casos, você precisa garantir que o banco de dados de origem seja compatível com o Banco de Dados SQL do Azure usando o [DMA (Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595). O Banco de Dados SQL V12 está se aproximando da [paridade de recursos](sql-database-features.md) com o SQL Server, além dos problemas relacionados às operações em nível do servidor e entre bancos de dados. Os bancos de dados e os aplicativos que dependem de [funções com suporte parcial ou sem suporte](sql-database-transact-sql-information.md) precisarão de alguma [reengenharia que corrija essas incompatibilidades](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) para que o banco de dados SQL Server possa ser migrado.

> [!NOTE]
> Para migrar um banco de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para o Banco de Dados SQL do Azure, confira [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2016/12/22/released-sql-server-migration-assistant-ssma-v7-2/).
> 

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Método 1: migração com tempo de inatividade durante a migração

 Use esse método se você puder ter algum tempo de inatividade ou se estiver executando um teste de migração de um banco de dados de produção para migração posterior. Para ver um tutorial, consulte [Migrar um Banco de Dados do SQL Server](sql-database-migrate-your-sql-server-database.md).

A lista a seguir contém o fluxo de trabalho geral para uma migração de banco de dados SQL Server usando esse método.

  ![Diagrama de migração do VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. Avalie o banco de dados em termos de compatibilidade usando a versão mais recente do [DMA (Assistente de Migração de Dados)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Prepare as correções necessárias como scripts Transact-SQL.
3. Faça uma cópia transacionalmente consistente do banco de dados de origem que está sendo migrado e verifique se não há mais alterações sendo feitas no banco de dados de origem (ou você pode aplicar essas alterações manualmente após a migração). Há vários métodos para fechar um banco de dados para novas sessões, desde desabilitar a conectividade do cliente até criar um [instantâneo do banco de dados](https://msdn.microsoft.com/library/ms175876.aspx).
4. Implante os scripts Transact-SQL para aplicar as correções à cópia do banco de dados.
5. [Exporte](sql-database-export.md) a cópia do banco de dados para um arquivo .BACPAC em uma unidade local.
6. [Importe](sql-database-import.md) o arquivo .BACPAC como um novo banco de dados Azure SQL usando qualquer uma das várias ferramentas de importação de BACPAC, sendo que SQLPackage.exe é a ferramenta recomendada para obter o melhor desempenho.

### <a name="optimizing-data-transfer-performance-during-migration"></a>Otimizando o desempenho de transferência de dados durante a migração 

A lista a seguir contém recomendações para melhorar o desempenho durante o processo de importação.

* Escolha o mais alto nível de serviço e nível de desempenho que seu orçamento permitir para maximizar o desempenho de transferência. Você pode reduzir verticalmente após a migração para economizar dinheiro. 
* Minimize a distância entre o arquivo .BACPAC e o data center de destino.
* Desabilitar estatísticas automaticamente durante a migração
* Índices e tabelas de partição
* Descartar exibições indexadas e recriá-las após a conclusão
* Remova dados históricos raramente consultados para outro banco de dados e migre esses dados históricos para um banco de dados Azure SQL separado. Em seguida, você pode consultar esses dados históricos usando [consultas elásticas](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Otimizar o desempenho após a migração ser concluída

[Atualize as estatísticas](https://msdn.microsoft.com/library/ms187348.aspx) com uma verificação completa após a migração ser concluída.

## <a name="method-2-use-transactional-replication"></a>Método 2: usar replicação transacional

Quando não houver a possibilidade de remover seu banco de dados do SQL Server da produção durante a migração, você poderá usar a replicação transacional do SQL Server como sua solução de migração. Para usar esse método, o banco de dados de origem deve atender a [requisitos para replicação transacional](https://msdn.microsoft.com/library/mt589530.aspx) e ser compatível com o banco de dados SQL. Para obter informações sobre a replicação do SQL com o AlwaysOn, consulte [Configurar a replicação para Grupos de Disponibilidade AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Para usar esta solução, você pode configurar o Banco de Dados SQL do Azure como um assinante da instância do SQL Server que você deseja migrar. O distribuidor de replicação transacional sincroniza os dados do banco de dados a ser sincronizado (o editor), enquanto as novas transações continuam a ocorrer. 

Com a replicação transacional, todas as alterações de dados ou esquema aparecem no seu Banco de Dados SQL do Azure. Quando a sincronização for concluída e você estiver pronto para migrar, altere a cadeia de conexão de seus aplicativos para apontá-los para seu Banco de Dados SQL do Azure. Assim que a replicação transacional realizar todas as alterações restantes no banco de dados e todos os aplicativos apontarem para o Banco de Dados do Azure, você poderá desinstalar a replicação transacional. O Banco de Dados SQL do Azure agora é o sistema de produção.

 ![Diagrama do SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Você também pode usar a replicação transacional para migrar um subconjunto do banco de dados de origem. A publicação que você replica no Banco de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas no banco de dados que está sendo replicado. Para cada tabela sendo replicada, você poderá limitar os dados a um subconjunto de linhas e/ou um subconjunto de colunas.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migração para o Banco de Dados SQL usando o fluxo de trabalho da Replicação de Transação

> [!IMPORTANT]
> Você deve usar a versão mais recente do SQL Server Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. Versões anteriores do SQL Server Management Studio não podem configurar o Banco de Dados SQL como um assinante. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Configurar a distribuição
   -  [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Criar a publicação
   -  [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Criar a assinatura
   -  [Usando o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Usando o Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

### <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Algumas dicas e diferenças da migração para o Banco de Dados SQL

1. Usar um distribuidor local 
   - Isso causa um impacto de desempenho no servidor. 
   - Se o impacto de desempenho for inaceitável, você poderá usar outro servidor, mas isso adicionará complexidade ao gerenciamento e à administração.
2. Ao selecionar uma pasta de instantâneo, verifique se a pasta selecionada é grande o suficiente para manter um BCP de cada tabela que você deseja replicar. 
3. A criação de instantâneos bloqueia as tabelas associadas até ser concluída. Portanto, agende o instantâneo adequadamente. 
4. Há suporte apenas para assinaturas push no Banco de Dados SQL do Azure. Você só pode adicionar assinantes do banco de dados de origem.

## <a name="resolving-database-migration-compatibility-issues"></a>Resolvendo problemas de compatibilidade de migração do banco de dados
Há uma grande variedade de problemas de compatibilidade que você pode encontrar, dependendo da versão do SQL Server no banco de dados de origem e da complexidade do banco de dados que você está migrando. Versões anteriores do SQL Server têm mais problemas de compatibilidade. Use os recursos a seguir, além de uma pesquisa direcionada na Internet, usando o mecanismo de pesquisa de sua preferência:

* [Recursos de banco de dados do SQL Server sem suporte no Banco de Dados SQL do Azure](sql-database-transact-sql-information.md)
* [Discontinued Database Engine Functionality in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Discontinued Database Engine Functionality in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Discontinued Database Engine Functionality in SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Discontinued Database Engine Functionality in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Discontinued Database Engine Functionality in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Além de pesquisar na Internet e usar esses recursos, use os [fóruns da comunidade do SQL Server no MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou o [StackOverflow](http://stackoverflow.com/).

## <a name="next-steps"></a>Próximas etapas
* Use o script no blog dos Engenheiros EMEA do Azure SQL para [Monitorar o uso de tempdb durante migração](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Use o script no blog dos Engenheiros EMEA do Azure SQL para [Monitorar o espaço de log de transações do banco de dados enquanto a migração está ocorrendo](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados do Azure SQL usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Para obter informações sobre como trabalhar com a hora UTC após a migração, confira [Modificando o fuso horário padrão para o fuso horário local](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
* Para obter informações sobre como alterar o idioma padrão de um banco de dados após a migração, confira [Como alterar o idioma padrão do Banco de Dados Azure SQL](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).


