---
title: Exemplos de script do Azure PowerShell para Banco de Dados SQL | Microsoft Docs
description: Exemplos de script do Azure PowerShell para ajudá-lo a criar e gerenciar servidores do Banco de Dados SQL do Azure, pools elásticos, bancos de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: c32803fec6107e39a0b8329adaee072e3e291575
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567065"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Amostras do Azure PowerShell para o Banco de Dados SQL do Azure

Banco de Dados SQL do Azure permitem que você configure seus bancos de dados, as instâncias e os pools usando o Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o AZ PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="single-database-and-elastic-pools"></a>Banco de Dados Individual e pools Elásticos

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Banco de Dados SQL do Azure.

| |  |
|---|---|
|**Criar e configurar bancos de dados únicos e pools elásticos**||
| [Criar um banco de dados individual e configurar uma regra de firewall do servidor de banco de dados](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este exemplo de script do PowerShell cria um Banco de Dados SQL do Azure e configura uma regra de firewall no nível do servidor. |
| [Criar pools Elásticos e mover bancos de dados em pools](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria pools elásticos do Banco de Dados SQL do Azure, move os bancos de dados em pool e altera os tamanhos da computação.|
|**Configurar a replicação geográfica e o failover**||
| [Configurar e fazer failover de um banco de dados individual usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell configura a replicação geográfica ativa para um único Banco de Dados SQL do Azure e faz o failover desse banco de dados para a réplica secundária. |
| [Configurar e fazer failover de um banco de dados em pool usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell configura a replicação geográfica ativa para um único Banco de Dados SQL do Azure em um pool elástico SQL e faz o failover dele para a réplica secundária. |
|**Configurar um grupo de failover**||
| [Configurar um grupo de failover para um banco de dados individual](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria um banco de dados e um grupo de failover, adiciona o banco de dados ao grupo de failover e testa o failover no servidor secundário. | 
|**Dimensionar um banco de dados individual e um pool elástico**||
| [Dimensionar um banco de dados individual](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitora as métricas de desempenho de um Banco de Dados SQL do Azure, dimensiona-o para um tamanho da computação mais alto e cria uma regra de alerta em uma das métricas de desempenho. |
| [Dimensionar um pool elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitora as métricas de desempenho de um pool elástico do Banco de Dados SQL do Azure, dimensiona-o para um tamanho da computação mais alto e cria uma regra de alerta em uma das métricas de desempenho. |
| **Auditoria e detecção de ameaças** |
| [Configurar auditoria e detecção de ameaças](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell configura políticas de detecção de ameaças e auditoria para um Banco de Dados SQL do Azure. |
| **Restaurar, copiar e importar um banco de dados**||
| [Restaurar um banco de dados](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell restaura um Banco de Dados SQL do Azure de um backup com redundância geográfica e restaura um Banco de Dados SQL do Azure excluído para o backup mais recente. |
| [Copiar um banco de dados para o novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell cria uma cópia do Banco de Dados SQL do Azure existente em um novo Azure SQL Server. |
| [Importar um banco de dados de um arquivo bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell importa um banco de dados para um SQL Server do Azure de um arquivo bacpac. |
| **Sincronizar dados entre bancos de dados**||
| [Sincronizar dados entre bancos de dados SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados para sincronização entre vários bancos de dados SQL do Azure. |
| [Sincronizar dados entre o Banco de Dados SQL e o SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados para sincronização entre um banco de dados SQL do Azure e um banco de dados do SQL Server local. |
| [Atualizar o esquema de sincronização da Sincronização de Dados SQL](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell adiciona ou remove itens do esquema de sincronização da Sincronização de Dados. |
|||

Saiba mais sobre a [API do Azure PowerShell de Banco de Dados Individual](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instância Gerenciada

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Banco de Dados SQL do Azure – Instância Gerenciada.

| |  |
|---|---|
|**Criar e configurar instâncias gerenciadas**||
| [Criar e gerenciar uma Instância Gerenciada](scripts/sql-database-create-configure-managed-instance-powershell.md) | Este script do PowerShell mostra como criar e gerenciar uma instância gerenciada usando o Azure PowerShell |
| [Criar e gerenciar uma Instância Gerenciada usando o modelo do Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell mostra como criar e gerenciar uma Instância Gerenciada usando o Azure PowerShell e o modelo do Azure Resource Manager.|
| [Restaurar um banco de dados em uma Instância Gerenciada em outra região geográfica](scripts/sql-managed-instance-restore-geo-backup.md) | Este script do PowerShell faz um backup de um banco de dados e o restaura em outra região. Isso é conhecido como cenário de recuperação de desastre de restauração geográfica. |
| **Configurar TDE (Transparent Data Encryption)**||
| [Gerenciar Transparent Data Encryption em uma Instância Gerenciada usando sua própria chave do Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a TDE (Transparent Data Encryption) no cenário de Bring Your Own Key para instância gerenciada do SQL usando uma chave do Azure Key Vault|
|||

Saiba mais sobre a [API do Azure PowerShell da Instância Gerenciada](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

## <a name="additional-resources"></a>Recursos adicionais

Os exemplos listados nesta página usam os [cmdlets do Banco de Dados SQL do Azure](/powershell/module/az.sql/) para criar e gerenciar recursos do SQL do Azure. Cmdlets adicionais para executar consultas e realizar muitas tarefas de banco de dados estão localizados no módulo [sqlserver](/powershell/module/sqlserver/). Para obter mais informações, confira [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
