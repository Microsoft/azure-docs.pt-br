---
title: Exemplos de script da CLI do Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Exemplos de script da CLI do Azure para ajudar você a criar e gerenciar a Instância Gerenciada de SQL do Azure e o Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 439167f29bb53d4a6e90b95826faa56e3c3170da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563334"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Exemplos da CLI do Azure para Banco de Dados SQL do Azure e Instância Gerenciada de SQL 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Você pode configurar o Banco de Dados SQL do Azure e a Instância Gerenciada de SQL usando a <a href="/cli/azure">CLI do Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

# <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/single-database)

A tabela a seguir inclui links para exemplos de scripts da CLI do Azure para gerenciar bancos de dados em pool e individuais no Banco de Dados SQL do Azure. 

|Área|Descrição|
|---|---|
|**Criar bancos de dados no Banco de dados SQL do Azure**||
| [Criar um Banco de Dados individual e configurar uma regra de firewall](scripts/create-and-configure-database-cli.md) | Cria um Banco de Dados SQL e configura uma regra de firewall no nível do servidor. |
| [Criar pools Elásticos e mover bancos de dados em pools](scripts/move-database-between-elastic-pools-cli.md) | Cria pools elásticos, move bancos de dados em pool e altera os tamanhos da computação. |
|**Escalar bancos de dados no Banco de dados SQL do Azure**||
| [Dimensionar um banco de dados individual](scripts/monitor-and-scale-database-cli.md) | Escala um banco de dados no Banco de Dados SQL do Azure para um tamanho da computação diferente depois de consultar as informações de tamanho do banco de dados. |
| [Dimensionar um pool elástico](scripts/scale-pool-cli.md) | Dimensiona um pool elástico do SQL para um tamanho da computação diferente. |
|**Configurar a replicação geográfica e o failover**||
| [Adicionar um banco de dados individual a um grupo de failover](scripts/add-database-to-failover-group-cli.md)| Cria um banco de dados e um grupo de failover, adiciona o banco de dados ao grupo de failover e testa o failover no servidor secundário. |
| [Configurar um grupo de failover para um pool elástico](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Cria um banco de dados, adiciona-o a um pool elástico, adiciona o pool elástico ao grupo de failover e testa o failover para o servidor secundário. |
| [Configurar e fazer failover de um banco de dados individual usando a replicação geográfica ativa](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configura a replicação geográfica ativa para um banco de dados no Banco de Dados SQL do Azure e faz o failover desse banco de dados para a réplica secundária. |
| [Configurar e fazer failover de um banco de dados em pool usando a replicação geográfica ativa](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configura a replicação geográfica ativa para um banco de dados em um pool elástico e faz o failover desse banco de dados para a réplica secundária. |
| **Auditoria e detecção de ameaças** |
| [Configurar auditoria e detecção de ameaças](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Configura políticas de detecção de ameaças e auditoria para um banco de dados no Banco de Dados SQL do Azure. |
| **Fazer backup, restaurar, copiar e importar um banco de dados**||
| [Fazer backup de um banco de dados](../../sql-database/scripts/sql-database-backup-database-cli.md)| Faz backup de um banco de dados no Banco de Dados SQL em um backup de armazenamento do Azure. |
| [Restaurar um banco de dados](../../sql-database/scripts/sql-database-restore-database-cli.md)| Restaura um banco de dados no Banco de Dados SQL com base em um backup com redundância geográfica e restaura um banco de dados excluído para o backup mais recente. |
| [Copiar um banco de dados para um novo servidor](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Cria uma cópia de um banco de dados existente no Banco de Dados SQL em um novo servidor. |
| [Importar um banco de dados de um arquivo BACPAC](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importa um banco de dados de um arquivo BACPAC para o Banco de Dados SQL. |
|||

Saiba mais sobre a [API da CLI do Azure de banco de dados individual](single-database-manage.md#the-azure-cli).

# <a name="azure-sql-managed-instance"></a>[Instância Gerenciada do SQL do Azure](#tab/managed-instance)

A tabela a seguir inclui links para exemplos de scripts da CLI do Azure para a Instância Gerenciada de SQL do Azure.

|Área|Descrição|
|---|---|
| **Criar uma Instância Gerenciada de SQL**||
| [Criar uma Instância Gerenciada de SQL](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Cria uma Instância Gerenciada de SQL. |
| **Configurar TDE (Transparent Data Encryption)**||
| [Gerenciar Transparent Data Encryption em uma Instância Gerenciada de SQL usando o Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configura o TDE (Transparent Data Encryption) na Instância Gerenciada de SQL usando o Azure Key Vault com vários cenários principais. |
|**Configurar um grupo de failover**||
| [Configurar um grupo de failover para uma Instância Gerenciada de SQL](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Cria duas instâncias da Instância Gerenciada de SQL, adiciona-as a um grupo de failover e, em seguida, testa o failover da Instância Gerenciada de SQL primária para a Instância Gerenciada de SQL secundária. |
|||

Para obter exemplos adicionais da Instância Gerenciada de SQL, confira os scripts [criar](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli), [atualizar](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli), [mover um banco de dados](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance) e [trabalhar com](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Saiba mais sobre a [API da CLI do Azure da Instância Gerenciada de SQL](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances).

---