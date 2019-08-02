---
title: Exemplo de PowerShell - Atualizar esquema de sincronização para Sincronização de Dados SQL | Microsoft Docs
description: Script de exemplo do Azure PowerShell para atualizar o esquema de sincronização para Sincronização de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 2bf782e1241f4d88beb3d52eefe80511375e800a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569752"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente

Este exemplo de PowerShell atualiza o esquema de sincronização em um grupo de sincronização da Sincronização de Dados SQL existente. Quando você estiver sincronizando várias tabelas, este script ajuda a atualizar o esquema de sincronização com eficiência. Este exemplo demonstra o uso do script **UpdateSyncSchema**, que está disponível no GitHub como [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o AZ PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](../sql-database-sync-data.md).

> [!IMPORTANT]
> No momento, a Sincronização de Dados SQL do Azure **não** dá suporte à Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="sample-script"></a>Script de exemplo

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Exemplo 1: adicionar todas as tabelas no esquema de sincronização

O exemplo a seguir atualiza o esquema de banco de dados e adiciona todas as tabelas válidas no banco de dados hub ao esquema de sincronização.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Exemplo 2: adicionar e remover tabelas e colunas

O exemplo a seguir adiciona `[dbo].[Table1]` e `[dbo].[Table2].[Column1]` ao esquema de sincronização e remove `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parâmetros de script

O script **UpdateSyncSchema** tem os seguintes parâmetros:

| Parâmetro | Observações |
|---|---|
| $SubscriptionId | A assinatura na qual o grupo de sincronização é criado. |
| $ResourceGroupName | O grupo de recursos no qual o grupo de sincronização é criado.|
| $ServerName | O nome do servidor do banco de dados hub.|
| $DatabaseName | O nome do banco de dados hub. |
| $SyncGroupName | O nome do grupo de sincronização. |
| $MemberName | Se você deseja carregar o esquema de banco de dados do membro em vez de sincronização em vez do banco de dados hub, especifique o nome do membro. Se você deseja carregar o esquema de banco de dados do hub, deixe esse parâmetro em branco. |
| $TimeoutInSeconds | Tempo limite quando o script atualiza o esquema de banco de dados. O padrão é 900 segundos. |
| $RefreshDatabaseSchema | Especifique se o script precisa atualizar o esquema de banco de dados. Se o esquema de banco de dados mudou em relação à configuração anterior (por exemplo, se você adicionou uma nova tabela ou uma nova coluna), você precisa atualizar o esquema antes de você reconfigurá-lo. O padrão é falso. |
| $AddAllTables | Se esse valor for true, todas as colunas e tabelas válidas são adicionadas ao esquema de sincronização. Os valores de $TablesAndColumnsToAdd e $TablesAndColumnsToRemove são ignorados. |
| $TablesAndColumnsToAdd | Especifique tabelas ou colunas a serem adicionadas ao esquema de sincronização. Cada nome de tabela ou coluna deve ser totalmente delimitado com o nome do esquema. Por exemplo, `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabela ou coluna podem ser especificados e separados por vírgulas (,). |
| $TablesAndColumnsToRemove | Especifique tabelas ou colunas a serem removidas do esquema de sincronização. Cada nome de tabela ou coluna deve ser totalmente delimitado com o nome do esquema. Por exemplo, `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabela ou coluna podem ser especificados e separados por vírgulas (,). |
|||

## <a name="script-explanation"></a>Explicação sobre o script

O script **UpdateSyncSchema** usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Retorna informações sobre um grupo de sincronização. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Atualiza um grupo de sincronização. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Retorna informações sobre um membro de sincronização. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Retorna informações sobre um esquema de sincronização. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Atualiza um esquema de sincronização. |
|||

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).

Para saber mais sobre a Sincronização de Dados SQL, veja:

-   Visão geral - [Sincronize dados em vários bancos de dados locais e na nuvem com o Azure SQL Data Sync](../sql-database-sync-data.md)
-   Configurar sincronização de dados
    - No portal - [Tutorial: Configurar sincronização de dados SQL para sincronizar dados entre o banco de dados SQL do Azure e SQL Server local](../sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](sql-database-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](sql-database-sync-data-between-azure-onprem.md)
-   Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](../sql-database-data-sync-agent.md)
-   Práticas recomendadas - [Práticas recomendadas para a Sincronização de Dados SQL do Azure](../sql-database-best-practices-data-sync.md)
-   Monitor – [monitore a Sincronização de Dados SQL com logs do Azure Monitor](../sql-database-sync-monitor-oms.md)
-   Solucionar problemas - [Solucionar problemas com o SQL Data Sync do Azure](../sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com Transact-SQL - [Automatize a replicação de alterações de esquema no Azure SQL Data Sync](../sql-database-update-sync-schema.md)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](../sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
