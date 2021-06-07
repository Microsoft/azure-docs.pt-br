---
title: Exemplos de script do Azure PowerShell
description: Use exemplos de script do Azure PowerShell para ajudar você a criar e gerenciar recursos de Instância Gerenciada de SQL do Azure e do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 7d732f0e42bc5eef8f72a2b9ce2adee28deb9a67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690956"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Exemplos do Azure PowerShell para Banco de Dados SQL do Azure e Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O Banco de Dados SQL do Azure e a Instância Gerenciada de SQL do Azure permitem que você configure seus bancos de dados, as instâncias e os pools usando o Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o AZ PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/single-database)

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Banco de Dados SQL do Azure.

|Link|Descrição|
|---|---|
|**Criar e configurar bancos de dados individuais e pools elásticos**||
| [Criar um banco de dados individual e configurar uma regra de firewall no nível do servidor](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria um banco de dados individual e configura uma regra de firewall de IP no nível do servidor. |
| [Criar pools Elásticos e mover bancos de dados em pools](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria pools elásticos, move os bancos de dados em pool e altera os tamanhos da computação.|
|**Configurar a replicação geográfica e o failover**||
| [Configurar e fazer failover de um banco de dados individual usando a replicação geográfica ativa](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a replicação geográfica ativa para um banco de dados individual e faz o failover desse banco de dados para a réplica secundária. |
| [Configurar e fazer failover de um banco de dados em pool usando a replicação geográfica ativa](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a replicação geográfica ativa para um banco de dados em um pool elástico e faz o failover desse banco de dados para a réplica secundária. |
|**Configurar um grupo de failover**||
| [Configurar um grupo de failover para um banco de dados individual](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria um banco de dados e um grupo de failover, adiciona o banco de dados ao grupo de failover e testa o failover no servidor secundário. |
| [Configurar um grupo de failover para um pool elástico](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria um banco de dados, adiciona-o a um pool elástico, adiciona o pool elástico ao grupo de failover e testa o failover para o servidor secundário. |
|**Dimensionar um banco de dados individual e um pool elástico**||
| [Dimensionar um banco de dados individual](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitora as métricas de desempenho de um banco de dados individual, dimensiona-o para um tamanho da computação maior e cria uma regra de alerta em uma das métricas de desempenho. |
| [Dimensionar um pool elástico](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitora as métricas de desempenho de um pool elástico, escala-o para um tamanho da computação maior e cria uma regra de alerta em uma das métricas de desempenho. |
| **Auditoria e detecção de ameaças** |
| [Configurar auditoria e detecção de ameaças](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura políticas de detecção de ameaças e auditoria para um banco de dados. |
| **Restaurar, copiar e importar um banco de dados**||
| [Restaurar um banco de dados](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell restaura um banco de dados com base em um backup com redundância geográfica e restaura um banco de dados excluído para o backup mais recente. |
| [Copiar um banco de dados para um novo servidor](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell cria uma cópia de um banco de dados existente em um novo servidor. |
| [Importar um banco de dados de um arquivo bacpac](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell importa um banco de dados de um arquivo bacpac para um Banco de Dados SQL do Azure. |
| **Sincronizar dados entre bancos de dados**||
| [Sincronizar dados entre bancos de dados](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados para sincronização entre vários bancos de dados no Banco de Dados SQL do Azure. |
| [Sincronizar dados entre o Banco de Dados SQL e o SQL Server local](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados para sincronização entre um banco de dados no Banco de Dados SQL do Azure e um banco de dados do SQL Server local. |
| [Atualizar o esquema de sincronização da Sincronização de Dados SQL](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell adiciona ou remove itens do esquema de sincronização da Sincronização de Dados. |
|||

Saiba mais sobre a [API do Azure PowerShell de banco de dados individual](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Instância Gerenciada do SQL do Azure](#tab/managed-instance)

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para a Instância Gerenciada de SQL do Azure.

|Link|Descrição|
|---|---|
|**Criar e configurar instâncias gerenciadas**||
| [Criar e gerenciar uma instância gerenciada](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Este script do PowerShell mostra como criar e gerenciar uma instância gerenciada usando o Azure PowerShell. |
| [Criar e gerenciar uma instância gerenciada usando o modelo do Azure Resource Manager](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell mostra como criar e gerenciar uma instância gerenciada usando o Azure PowerShell e o modelo do Azure Resource Manager.|
| [Restaurar um banco de dados em uma instância gerenciada em outra região geográfica](../managed-instance/scripts/restore-geo-backup.md) | Este script do PowerShell faz um backup de um banco de dados e o restaura em outra região. Isso é conhecido como cenário de recuperação de desastre de restauração geográfica. |
| **Configurar a Transparent Data Encryption**||
| [Gerenciar a Transparent Data Encryption em uma instância gerenciada usando sua própria chave do Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a Transparent Data Encryption em um cenário de Bring Your Own Key para instância Gerenciada de SQL do Azure usando uma chave do Azure Key Vault.|
|**Configurar um grupo de failover**||
| [Configurar um grupo de failover para uma instância gerenciada](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Esse script do PowerShell cria duas instâncias gerenciadas, adiciona-as a um grupo de failover e, em seguida, testa o failover da instância gerenciada primária para a instância gerenciada secundária. |
|||

Saiba mais sobre os [cmdlets do PowerShell para a Instância Gerenciada de SQL do Azure](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances).

---

## <a name="additional-resources"></a>Recursos adicionais

Os exemplos listados nesta página usam os [cmdlets do PowerShell](/powershell/module/az.sql/) para criar e gerenciar recursos de SQL do Azure. Cmdlets adicionais para executar consultas e realizar muitas tarefas de banco de dados estão localizados no módulo [sqlserver](/powershell/module/sqlserver/). Para obter mais informações, confira [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).