---
title: 'PowerShell: Atualizar esquema de sincronização da Sincronização de Dados SQL'
description: Script de exemplo do Azure PowerShell para atualizar o esquema de sincronização para Sincronização de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792711"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de Azure PowerShell atualiza o esquema de sincronização em um grupo de sincronização da Sincronização de Dados SQL existente. Quando você estiver sincronizando várias tabelas, este script ajuda a atualizar o esquema de sincronização com eficiência. Este exemplo demonstra o uso do script **UpdateSyncSchema**, que está disponível no GitHub como [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Quando você opta por instalar e usar o PowerShell localmente, este tutorial exige o Az PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> No momento, a Sincronização de Dados SQL não dá suporte à Instância Gerenciada de SQL do Azure.

## <a name="examples"></a>Exemplos

### <a name="add-all-tables-to-the-sync-schema"></a>Adicionar todas as tabelas no esquema de sincronização

O exemplo a seguir atualiza o esquema de banco de dados e adiciona todas as tabelas válidas no banco de dados hub ao esquema de sincronização.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Adicionar e remover tabelas e colunas

O exemplo a seguir adiciona `[dbo].[Table1]` e `[dbo].[Table2].[Column1]` ao esquema de sincronização e remove `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parâmetros de script

O script **UpdateSyncSchema** tem os seguintes parâmetros:

| Parâmetro | Observações |
|---|---|
| $subscriptionId | A assinatura na qual o grupo de sincronização é criado. |
| $resourceGroupName | O grupo de recursos no qual o grupo de sincronização é criado.|
| $serverName | O nome do servidor do banco de dados hub.|
| $databaseName | O nome do banco de dados hub. |
| $syncGroupName | O nome do grupo de sincronização. |
| $memberName | Se você deseja carregar o esquema de banco de dados do membro em vez de sincronização em vez do banco de dados hub, especifique o nome do membro. Se você deseja carregar o esquema de banco de dados do hub, deixe esse parâmetro em branco. |
| $timeoutInSeconds | Tempo limite quando o script atualiza o esquema de banco de dados. O padrão é 900 segundos. |
| $refreshDatabaseSchema | Especifique se o script precisa atualizar o esquema de banco de dados. Se o esquema de banco de dados mudou em relação à configuração anterior (por exemplo, se você adicionou uma nova tabela ou uma nova coluna), você precisa atualizar o esquema antes de você reconfigurá-lo. O padrão é false. |
| $addAllTables | Se esse valor for true, todas as colunas e tabelas válidas são adicionadas ao esquema de sincronização. Os valores de $TablesAndColumnsToAdd e $TablesAndColumnsToRemove são ignorados. |
| $tablesAndColumnsToAdd | Especifique tabelas ou colunas a serem adicionadas ao esquema de sincronização. Cada nome de tabela ou coluna deve ser totalmente delimitado com o nome do esquema. Por exemplo, `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabela ou coluna podem ser especificados e separados por uma vírgula (,). |
| $tablesAndColumnsToRemove | Especifique tabelas ou colunas a serem removidas do esquema de sincronização. Cada nome de tabela ou coluna deve ser totalmente delimitado com o nome do esquema. Por exemplo, `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabela ou coluna podem ser especificados e separados por uma vírgula (,). |

## <a name="script-explanation"></a>Explicação sobre o script

O script **UpdateSyncSchema** usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | Retorna informações sobre um grupo de sincronização. |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | Atualiza um grupo de sincronização. |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | Retorna informações sobre um membro da sincronização. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | Retorna informações sobre um esquema de sincronização. |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | Atualiza um esquema de sincronização. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../powershell-script-content-guide.md).

Para obter mais informações sobre a Sincronização de Dados SQL, consulte:

- Visão geral – [Sincronizar dados entre o Banco de Dados SQL do Azure e o SQL Server com a Sincronização de Dados SQL no Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Configurar sincronização de dados
    - Usar o portal do Azure – [Tutorial: Configurar a Sincronização de Dados SQL para sincronizar dados entre o BAnco de Dados SQL do Azure e SQL Server](../sql-data-sync-sql-server-configure.md)
    - Usar o PowerShell
        -  [Usar o PowerShell para sincronizar dados entre vários bancos de dados no Banco de Dados SQL do Azure](sql-data-sync-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar dados entre um Banco de Dados SQL do Azure e um SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent – [Data Sync Agent para Sincronização de Dados SQL no Azure](../sql-data-sync-agent-overview.md)
- Melhores práticas – [Melhores práticas para a Sincronização de Dados SQL no Azure](../sql-data-sync-best-practices.md)
- Monitor – [monitore a Sincronização de Dados SQL com logs do Azure Monitor](../monitor-tune-overview.md)
- Solucionar problemas – [Solucionar problemas com a Sincronização de Dados SQL no Azure](../sql-data-sync-troubleshoot.md)
- Atualizar o esquema de sincronização
    - Usar Transact-SQL – [Automatizar a replicação de alterações de esquema da Sincronização de Dados SQL no Azure](../sql-data-sync-update-sync-schema.md)

Para saber mais sobre Bancos de Dados SQL, confira:

- [Visão geral do Banco de Dados SQL](../sql-database-paas-overview.md)
- [Gerenciamento de ciclo de vida do banco de dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))