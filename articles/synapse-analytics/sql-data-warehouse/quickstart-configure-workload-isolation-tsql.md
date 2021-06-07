---
title: 'Início Rápido: Configurar isolamento da carga de trabalho – T-SQL'
description: Use o T-SQL para configurar o isolamento da carga de trabalho.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d69c1708e73ad7ce5610a0683835e9f304c3f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679747"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Início rápido: Configurar o isolamento de carga de trabalho em um pool de SQL dedicado usando T-SQL

Neste guia de início rápido, você criará rapidamente um classificador e um grupo de cargas de trabalho para reservar recursos para o carregamento de dados. O grupo de cargas de trabalho alocará 20% dos recursos do sistema para os carregamentos de dados.  O classificador de cargas de trabalho atribuirá solicitações ao grupo de cargas de trabalho do carregamento de dados.  Com 20% de reserva para carregamento de dados, esses recursos garantem a conformidade com os SLAs.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de uma instância do SQL do Synapse no Azure Synapse Analytics pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pressupõe que você já tem uma instância do SQL do Synapse no Azure Synapse e que você tem permissões de CONTROLE SOBRE O BANCO DE DADOS. Se precisar criar, use [Criar e conectar – portal](create-data-warehouse-portal.md) para criar um pool de SQL dedicado chamado **mySampleDataWarehouse**.

## <a name="create-login-for-dataloads"></a>Criar logon para carregamento de dados

Crie um logon de autenticação do SQL Server no banco de dados `master` usando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para "ELTLogin".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar usuário

[Create user](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), "ELTLogin", no mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Criar um grupo de cargas de trabalho

Crie um [grupo de cargas de trabalho](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) com 20% de reserva para carregamento de dados.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Crie um [classificador de cargas de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a fim de mapear o ELTLogin para o grupo de cargas de trabalho do carregamento de dados.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Exibir grupos de cargas de trabalho e classificadores e valores de runtime existentes

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Limpar os recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Você está sendo cobrado por unidades de data warehouse e pelos dados armazenados em seu pool de SQL dedicado. Esses recursos de computação e armazenamento são cobrados separadamente.

- Se desejar manter os dados no armazenamento, será possível pausar a computação quando você não estiver usando o pool de SQL dedicado. Ao pausar a computação, você será cobrado apenas pelo armazenamento de dados. Quando você estiver pronto para trabalhar com os dados, retome a computação.
- Se você quiser remover encargos futuros, poderá excluir o pool de SQL dedicado.

## <a name="next-steps"></a>Próximas etapas

- Você acabou de criar um grupo de cargas de trabalho. Execute algumas consultas como ELTLogin para ver o desempenho delas. Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para exibir as consultas e o grupo de cargas de trabalho atribuído.
- Para saber mais sobre o gerenciamento de cargas de trabalho SQL do Synapse, confira [Gerenciamento de Cargas de Trabalho](sql-data-warehouse-workload-management.md) e [Isolamento de Cargas de Trabalho](sql-data-warehouse-workload-isolation.md).
