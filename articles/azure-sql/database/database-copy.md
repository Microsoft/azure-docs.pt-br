---
title: Copiar um banco de dados
description: Crie uma cópia transacionalmente consistente de um banco de dados existente no banco de dados SQL do Azure no mesmo servidor ou em um servidor diferente.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: 1a86522975ffb7b5b2bd514402dd97a76aa2506e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014580"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Copiar uma cópia transacionalmente consistente de um banco de dados no banco de dados SQL do Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O banco de dados SQL do Azure fornece vários métodos para criar uma cópia de um [banco de dados](single-database-overview.md) existente no mesmo servidor ou em um servidor diferente. Você pode copiar um banco de dados usando portal do Azure, PowerShell, CLI do Azure ou T-SQL.

## <a name="overview"></a>Visão geral

Uma cópia de banco de dados é um instantâneo transacionalmente consistente do banco de dados de origem a partir de um ponto no tempo depois que a solicitação de cópia é iniciada. Você pode selecionar o mesmo servidor ou um servidor diferente para a cópia. Além disso, você pode optar por manter a redundância de backup, a camada de serviço e o tamanho de computação do banco de dados de origem, ou usar uma redundância de armazenamento de backup diferente e/ou o tamanho de computação na mesma camada de serviço ou em outra. Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Os logons, os usuários e as permissões no banco de dados copiado são gerenciados independentemente do banco de dados de origem. A cópia é criada usando a tecnologia de replicação geográfica. Depois que a propagação de réplica for concluída, o link de replicação geográfica será encerrado automaticamente. Todos os requisitos para usar a replicação geográfica se aplicam à operação de cópia do banco de dados. Consulte [visão geral da replicação geográfica ativa](active-geo-replication-overview.md) para obter detalhes.

> [!NOTE]
> A redundância de armazenamento de backup configurável do banco de dados SQL do Azure está disponível atualmente na visualização pública no sul do Brasil e geralmente disponível somente na região do sudeste asiático do Azure. Na visualização, se o banco de dados de origem for criado com redundância de armazenamento de backup com redundância local ou de zona, a cópia de banco de dados para um servidor em uma região diferente do Azure não terá suporte. 

## <a name="logins-in-the-database-copy"></a>Logons na cópia do banco de dados

Quando você copia um banco de dados para o mesmo servidor, os mesmos logons podem ser usados em ambos os bancos. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados do banco de dados.

Quando você copia um banco de dados para um servidor diferente, a entidade de segurança que iniciou a operação de cópia no servidor de destino torna-se o proprietário do novo banco de dados.

Independentemente do servidor de destino, todos os usuários do banco de dados, suas permissões e seus SIDs (identificadores de segurança) são copiados para a cópia do banco de dados. O uso de [usuários de banco](logins-create-manage.md) de dados independente para o acesso ao data garante que o banco de dado copiado tenha as mesmas credenciais de usuário, para que depois que a cópia for concluída, você possa acessá-la imediatamente com as mesmas credenciais.

Se você usar logons no nível do servidor para acessar os dados e copiar o banco de dado para um servidor diferente, o acesso baseado em logon talvez não funcione. Isso pode acontecer porque os logons não existem no servidor de destino ou porque as senhas e SIDs (identificadores de segurança) são diferentes. Para saber mais sobre como gerenciar logons ao copiar um banco de dados para um servidor diferente, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](active-geo-replication-security-configure.md). Depois que a operação de cópia para um servidor diferente for realizada com sucesso e antes que outros usuários sejam remapeados, somente o logon associado ao proprietário do banco de dados ou o administrador do servidor poderão fazer logon no banco de dados copiado. Para resolver logons e estabelecer o acesso a dados após a conclusão da operação de cópia, consulte [resolver logons](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Copiar usando o portal do Azure

Para copiar um banco de dados usando o Portal do Azure, abra a página do banco de dados e clique em **Copiar**.

   ![Cópia do banco de dados](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Copiar usando o PowerShell ou o CLI do Azure

Para copiar um banco de dados, use os exemplos a seguir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para o PowerShell, use o cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

A cópia do banco de dados é uma operação assíncrona, mas o banco de dados de destino é criado imediatamente depois que a solicitação é aceita. Se você precisar cancelar a operação de cópia enquanto ainda estiver em andamento, remova o banco de dados de destino usando o cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Para obter um exemplo completo de script do PowerShell, consulte [copiar um banco de dados para um novo servidor](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

A cópia do banco de dados é uma operação assíncrona, mas o banco de dados de destino é criado imediatamente depois que a solicitação é aceita. Se você precisar cancelar a operação de cópia enquanto ainda estiver em andamento, remova o banco de dados de destino usando o comando [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="copy-using-transact-sql"></a>Copiar usando Transact-SQL

Faça logon no banco de dados mestre com o logon de administrador do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia do banco de dados tenha sucesso, os logons que não são o administrador do servidor devem ser membros da `dbmanager` função. Para saber mais sobre logons e como se conectar ao servidor, confira [Gerenciar logons](logins-create-manage.md).

Comece a copiar o banco de dados de origem com o [criar banco de dados... COMO cópia da](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) instrução. A instrução T-SQL continua em execução até que a operação de cópia do banco de dados seja concluída.

> [!NOTE]
> Encerrar a instrução T-SQL não encerra a operação de cópia de banco de dados. Para encerrar a operação, descarte o banco de dados de destino.
>

> [!IMPORTANT]
> Selecionando redundância de armazenamento de backup ao usar o T-SQL criar banco de dados... Ainda não há suporte para a cópia do comando. 

### <a name="copy-to-the-same-server"></a>Copiar para o mesmo servidor

Faça logon no banco de dados mestre com o logon de administrador do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia de banco de dados tenha sucesso, os logons que não são o administrador do servidor devem ser membros da `dbmanager` função.

Esse comando copia o Banco de dados 1 para um novo banco de dados chamado Database2 (Banco de dados 2) no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Copiar para um pool elástico

Faça logon no banco de dados mestre com o logon de administrador do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia de banco de dados tenha sucesso, os logons que não são o administrador do servidor devem ser membros da `dbmanager` função.

Esse comando copia Database1 para um novo banco de dados chamado Database2 em um pool elástico chamado pool1. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

Database1 pode ser um banco de dados único ou em pool. Há suporte para a cópia entre diferentes pools de camada, mas algumas cópias entre camadas não terão sucesso. Por exemplo, você pode copiar um único banco de BD padrão ou elástico para um pool de uso geral, mas não pode copiar um banco de forma elástico padrão para um pool Premium. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Copiar para um servidor diferente

Faça logon no banco de dados mestre do servidor de destino onde o novo banco de dados deve ser criado. Use um logon que tenha o mesmo nome e senha que o proprietário do banco de dados de origem no servidor de origem. O logon no servidor de destino também deve ser um membro da `dbmanager` função ou ser o logon de administrador do servidor.

Esse comando copia o Database1 no servidor 1- para um novo banco de dados chamado Database2 no servidor 2. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Os firewalls de servidores devem ser configurados para permitir a conexão de entrada do IP do cliente que emite o T-SQL CREATE DATABASE... COMO cópia do comando.

### <a name="copy-to-a-different-subscription"></a>Copiar para uma assinatura diferente

Você pode usar as etapas na seção [copiar um banco de dados SQL para um servidor diferente](#copy-to-a-different-server) para copiar seu banco de dados para um servidor em uma assinatura diferente usando o T-SQL. Certifique-se de usar um logon que tenha o mesmo nome e senha que o proprietário do banco de dados de origem. Além disso, o logon deve ser um membro da `dbmanager` função ou de um administrador de servidor, nos servidores de origem e de destino.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> O [portal do Azure](https://portal.azure.com), o PowerShell e o CLI do Azure não dão suporte à cópia de banco de dados para uma assinatura diferente.

> [!TIP]
> A cópia de banco de dados usando o T-SQL dá suporte à cópia de um banco de dados de uma assinatura em um locatário do Azure diferente. Isso só tem suporte ao usar um logon de autenticação do SQL para fazer logon no servidor de destino.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorar o andamento da operação de cópia

Monitore o processo de cópia consultando os modos de exibição [Sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [Sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)e [Sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Enquanto a cópia estiver em andamento, a coluna **state_desc** da exibição sys.databases para o novo banco de dados é definida como **COPYING**.

* Se a cópia falhar, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **SUSPECT**. Execute a instrução DROP no novo banco de dados e tente novamente mais tarde.
* Se a cópia for bem-sucedida, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **ONLINE**. A cópia foi concluída e o novo banco de dados é um banco de dados normal, capaz de ser alterado de forma independente do banco de dados de origem.

> [!NOTE]
> Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) no novo banco de dados.

> [!IMPORTANT]
> Se você precisar criar uma cópia com um objetivo de serviço substancialmente menor do que a origem, o banco de dados de destino pode não ter recursos suficientes para concluir o processo de propagação e isso pode fazer com que a operação de cópia falhe. Nesse cenário, use uma solicitação de restauração geográfica para criar uma cópia em um servidor diferente e/ou em uma região diferente. Consulte [recuperar um banco de dados SQL do Azure usando backups de banco de dados](recovery-using-backups.md#geo-restore) para obter mais informações.

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Funções e permissões do RBAC do Azure para gerenciar a cópia do banco de dados

Para criar uma cópia de banco de dados, você precisará estar nas seguintes funções

* Proprietário da assinatura ou ter
* Função Colaborador do SQL Server ou
* Função personalizada nos bancos de dados de origem e de destino com a seguinte permissão:

   Microsoft. SQL/servidores/bancos de dados/ler Microsoft. SQL/servidores/bancos de dados/gravar

Para cancelar uma cópia de banco de dados, você precisará estar nas seguintes funções

* Proprietário da assinatura ou ter
* Função Colaborador do SQL Server ou
* Função personalizada nos bancos de dados de origem e de destino com a seguinte permissão:

   Microsoft. SQL/servidores/bancos de dados/ler Microsoft. SQL/servidores/bancos de dados/gravar

Para gerenciar a cópia de banco de dados usando o portal do Azure, você também precisará das seguintes permissões:

   Microsoft. Resources/subscriptions/Resources/Read Microsoft. Resources/subscriptions/Resources/inscrições/Write Microsoft. Resources/Implantations/Read Microsoft. Resources/Implantations/Write Microsoft. Resources/Implantations/operationstatuses/Read

Se você quiser ver as operações em implantações no grupo de recursos no portal, operações em vários provedores de recursos, incluindo operações SQL, você precisará dessas funções adicionais do Azure:

   Microsoft. Resources/subscriptions/resourcegroups/implantações/operações/ler Microsoft. Resources/subscriptions/resourcegroups/Implantations/operationstatuses/Read

## <a name="resolve-logins"></a>Resolver logons

Depois que o novo banco de dados estiver online no servidor de destino, use a instrução [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) para remapear os usuários do novo banco de dados para os logons no servidor de destino. Para resolver usuários órfãos, confira [Solução de problemas de usuários órfãos](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Consulte também [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](active-geo-replication-security-configure.md).

Todos os usuários no novo banco de dados mantêm as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados se torna o proprietário do novo banco de dados. Depois que a cópia for realizada com sucesso e antes que outros usuários sejam remapeados, somente o proprietário do banco de dados poderá fazer logon no novo banco de dados.

Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor diferente, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Erros de cópia de banco de dados

Os erros a seguir podem ser encontrados durante a cópia de um banco de dados no Banco de Dados SQL do Azure. Para saber mais, confira [Copiar um Banco de Dados SQL do Azure](database-copy.md).

| Código do erro | Severidade | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |O cliente com endereço IP “%.&#x2a;ls” está desabilitado temporariamente. |
| 40637 |16 |A criação da cópia do banco de dados está desabilitada no momento. |
| 40561 |16 |Falha na cópia do banco de dados. O banco de dados de origem ou de destino não existe. |
| 40562 |16 |Falha na cópia do banco de dados. O banco de dados de origem foi descartado. |
| 40563 |16 |Falha na cópia do banco de dados. O banco de dados de destino foi descartado. |
| 40564 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40565 |16 |Falha na cópia do banco de dados. Não é permitida mais de uma cópia simultânea do banco de dados com a mesma origem. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40566 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40567 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40568 |16 |Falha na cópia do banco de dados. O banco de dados de origem tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40569 |16 |Falha na cópia do banco de dados. O banco de dados de destino tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40570 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40571 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre logons, consulte [gerenciar logons](logins-create-manage.md) e [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](active-geo-replication-security-configure.md).
* Para exportar um banco de dados, consulte [Exportar o banco de dados para um BACPAC](database-export.md).
