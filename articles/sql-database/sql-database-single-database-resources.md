---
title: Banco de dados individual do Banco de Dados SQL do Azure | Microsoft Docs
description: Gerencie um banco de dados individual do SQL Azure.
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 70f512ef5742346df742e0718e2ccffd076a979e
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Gerenciar recursos para um banco de dados individual no Banco de Dados SQL do Azure

Com um único banco de dados, você determina a quantidade de recursos que o banco de dados exige para lidar com sua carga de trabalho na camada de serviço, no nível de desempenho e a quantidade de armazenamento que ele exige. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Gerenciar recursos de um banco de dados individual usando o Portal do Azure

Para definir ou alterar o nível de serviço, o nível de desempenho ou a quantidade de armazenamento para um banco de dados SQL do Azure novo ou existente usando o Portal do Azure, abra a janela **Configurar desempenho** para o banco de dados clicando em **Tipo de preço (DTUs de escala)** - conforme mostra a captura de tela a seguir. 

- Definir ou alterar o nível de serviço selecionando o nível de serviço para sua carga de trabalho. 
- Definir ou alterar o nível de desempenho (**DTUs**) dentro de um nível de serviço usando o controle deslizante de **DTU**.
- Definir ou alterar a quantidade de armazenamento para o nível de desempenho usando o controle deslizante de **Armazenamento**. 

![Definir o nível de serviço e o nível de desempenho](./media/sql-database-single-database-resources/change-service-tier.png)

> [!IMPORTANT]
> Revise [Limitações atuais de bancos de dados P11 e P15 com tamanho máximo de 4 TB](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) ao selecionar um nível de serviço P11 ou P15.
>

## <a name="manage-single-database-resources-using-powershell"></a>Gerenciar recursos de um banco de dados individual usando o PowerShell

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento dos Bancos de Dados SQL do Azure usando o PowerShell, use estes cmdlets do PowerShell. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Cmdlet | Descrição |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Cria um banco de dados |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtém um ou mais bancos de dados|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico. Por exemplo, use a propriedade **MaxSizeBytes** para definir o tamanho máximo de um banco de dados.|


> [!TIP]
> Para um script do PowerShell de exemplo que monitora as métricas de desempenho de um banco de dados, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho, consulte [Monitorar e escalar um banco de dados SQL individual usando PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Gerenciar recursos de um banco de dados individual usando a CLI do Azure

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento dos Bancos de Dados SQL do Azure usando a CLI do Azure, use estes cmdlets da [CLI do Azure do Banco de Dados SQL](/cli/azure/sql/db). Use o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerenciar pools elásticos de SQL, consulte [Pools elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descrição |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Cria uma regra de firewall de servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Lista as regras de firewall em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Mostra os detalhes de uma regra de firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Atualiza uma regra de firewall|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Exclui uma regra de firewall|


> [!TIP]
> Para ver um script de exemplo da CLI do Azure que escala um único Banco de Dados SQL do Azure para um nível de desempenho diferente depois de consultar as informações de tamanho do banco de dados, consulte [Usar a CLI para monitorar e escalar um único Banco de Dados SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Gerenciar recursos de um banco de dados individual usando Transact-SQL

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento dos Bancos de Dados SQL do Azure com Transact-SQL, use estes comando T-SQL. Você pode emitir esses comandos usando o Portal do Azure, o [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), o [Visual Studio Code](https://code.visualstudio.com/docs) ou qualquer outro programa que pode se conectar a um servidor de Banco de Dados SQL e passar comandos Transact-SQL. 

| Command | Descrição |
| --- | --- |
|[CREATE DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria um novo banco de dados. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica um Banco de Dados SQL do Azure. |
|[sys.database_service_objectives (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (nível de serviço), o objetivo do serviço (tipo de preço) e o nome do pool elástico, se houver um, para um Banco de Dados SQL do Azure ou um SQL Data Warehouse do Azure. Se estiver conectado ao banco de dados mestre em um servidor de Banco de Dados SQL do Azure, retornará informações sobre todos os bancos de dados. Para o SQL Data Warehouse do Azure, você deve estar conectado ao banco de dados mestre.|
|[sys.database_usage (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Lista o número, o tipo e a duração dos bancos de dados em um servidor de Banco de Dados SQL do Azure.|

O exemplo a seguir mostra o tamanho máximo de um banco de dados sendo alterado usando o comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Gerenciar recursos de um banco de dados individual usando a API REST

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento dos Bancos de Dados SQL do Azure, use estas solicitações da API REST.

| Command | Descrição |
| --- | --- |
|[Banco de Dados – Criar ou Atualizar](/rest/api/sql/databases/createorupdate)|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|[Bancos de Dados – Obter](/rest/api/sql/databases/get)|Obtém um banco de dados.|
|[Bancos de Dados – Obter por Pool Elástico](/rest/api/sql/databases/getbyelasticpool)|Obtém um banco de dados dentro de um pool elástico.|
|[Bancos de Dados – Obter por Pool Elástico Recomendado](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtém um banco de dados dentro de um pool elástico recomendado.|
|[Bancos de Dados – Listar por Pool Elástico](/rest/api/sql/databases/listbyelasticpool)|Retorna uma lista de bancos de dados em um pool elástico.|
|[Bancos de Dados – Listar por Pool Elástico Recomendado](/rest/api/sql/databases/listbyrecommendedelasticpool)|Retorna uma lista de bancos de dados dentro de um pool elástico recomendado.|
|[Bancos de Dados – Listar por Servidor](/rest/api/sql/databases/listbyserver)|Retorna uma lista de bancos de dados em um servidor.|
|[Banco de Dados - Atualização]((/rest/api/sql/databases/update)|Atualiza um banco de dados existente.|



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as camadas de serviço, níveis de desempenho e quantidades de armazenamento em [Camadas de serviço](sql-database-service-tiers.md).
- Saiba mais sobre pools elásticos em [Pools elásticos](sql-database-elastic-pool.md).
- Saiba mais sobre a [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
