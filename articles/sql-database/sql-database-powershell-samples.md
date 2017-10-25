---
title: Exemplos de script do Azure PowerShell para Banco de Dados SQL | Microsoft Docs
description: "Exemplos de script do Azure PowerShell para ajudá-lo a criar e gerenciar servidores de Banco de Dados SQL do Azure, pools elásticos, bancos de dados e firewalls."
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: 5a5f77b9adafe32e8559d0b3396febca4b191de3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Amostras do Azure PowerShell para o Banco de Dados SQL do Azure

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Banco de Dados SQL do Azure.

| |  |
|---|---|
|**Criar um banco de dados individual e um pool elástico**||
| [Criar um Banco de Dados individual e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este exemplo de script do PowerShell cria um Banco de Dados SQL do Azure e configura uma regra de firewall no nível do servidor. |
| [Criar pools Elásticos e mover bancos de dados em pools](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria pools elásticos do Banco de Dados SQL do Azure, move os bancos de dados em pool e altera os níveis de desempenho.|
|**Configurar a replicação geográfica e o failover**||
| [Configurar e fazer failover de um banco de dados individual usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell configura a replicação geográfica ativa para um único Banco de Dados SQL do Azure e faz o failover desse banco de dados para a réplica secundária. |
| [Configurar e fazer failover de um banco de dados em pool usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell configura a replicação geográfica ativa para um único Banco de Dados SQL do Azure em um pool elástico SQL e faz o failover dele para a réplica secundária. |
| [Configurar e realizar o failover de um grupo de failover para um banco de dados individual (versão prévia)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura um grupo de failover para uma instância de servidor de Banco de Dados SQL do Azure, adiciona um banco de dados ao grupo de failover e realiza o failover dele para o servidor secundário |
|**Dimensionar um banco de dados individual e um pool elástico**||
| [Dimensionar um banco de dados individual](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitora as métricas de desempenho de um Banco de Dados SQL do Azure, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho. |
| [Dimensionar um pool elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitora as métricas de desempenho de um pool elástico do Banco de Dados SQL do Azure, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho.  |
| **Auditoria e detecção de ameaças** |
| [Configurar auditoria e detecção de ameaças](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell configura políticas de detecção de ameaças e auditoria para um Banco de Dados SQL do Azure. |
| **Restaurar, copiar e importar um banco de dados**||
| [Restaurar um banco de dados](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell restaura um Banco de Dados SQL do Azure de um backup com redundância geográfica e restaura um Banco de Dados SQL do Azure excluído para o backup mais recente. |
| [Copiar um banco de dados para o novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell cria uma cópia do Banco de Dados SQL do Azure existente em um novo Azure SQL Server. |
| [Importar um banco de dados de um arquivo bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Esse script do PowerShell importa um banco de dados para um SQL Server do Azure de um arquivo bacpac. |
| **Sincronizar dados entre bancos de dados**||
| [Sincronizar dados entre bancos de dados SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados para sincronização entre vários bancos de dados SQL do Azure. |
| [Sincronizar dados entre o Banco de Dados SQL e o SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados para sincronização entre um banco de dados SQL do Azure e um banco de dados do SQL Server local. |
|||
|||
