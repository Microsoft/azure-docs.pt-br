---
title: 'Início Rápido: criar um classificador de carga de trabalho – T-SQL'
description: Use o T-SQL para criar um classificador de carga de trabalho com alta importância.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677361"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Início Rápido: Criar um classificador de carga de trabalho usando o T-SQL

Neste início rápido, você criará rapidamente um classificador de carga de trabalho com alta importância para o CEO da sua organização. Esse classificador de carga de trabalho permitirá que as consultas do CEO tenham precedência sobre outras consultas com menos importância na fila.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de uma instância de pool de SQL dedicado no Azure Synapse Analytics pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido pressupõe que você já tenha provisionado um pool de SQL dedicado no Azure Synapse Analytics e tenha permissões CONTROL DATABASE. Se precisar criar, use [Criar e conectar – portal](create-data-warehouse-portal.md) para criar um pool de SQL dedicado chamado **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Criar logon para TheCEO

Crie um logon de autenticação do SQL Server no banco de dados `master` usando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para “TheCEO”.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar usuário

[Criar usuário](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), "TheCEO", no mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Criar um [classificador de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para "TheCEO" com alta importância.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Exibir classificadores existentes

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Limpar os recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Você está sendo cobrado por unidades de data warehouse e pelos dados armazenados em seu pool de SQL dedicado. Esses recursos de computação e armazenamento são cobrados separadamente.

- Se desejar manter os dados no armazenamento, será possível pausar a computação quando você não estiver usando o pool de SQL dedicado. Ao pausar a computação, você será cobrado apenas pelo armazenamento de dados. Quando você estiver pronto para trabalhar com os dados, retome a computação.
- Se você quiser remover encargos futuros, poderá excluir o pool de SQL dedicado.

Siga estas etapas para limpar os recursos.

1. Entre no [portal do Azure](https://portal.azure.com), selecione seu pool de SQL dedicado.

    ![Limpar os recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para pausar a computação, selecione o botão **Pausar**. Quando o pool de SQL dedicado estiver em pausa, você verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o pool de SQL dedicado para que não seja cobrado pela computação ou pelo armazenamento, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Agora você criou um classificador de carga de trabalho. Execute algumas consultas como TheCEO para ver o desempenho delas. Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para exibir consultas e a importância atribuída.
- Para saber mais sobre o gerenciamento de cargas de trabalho do pool de SQL dedicado, confira [Importância da carga de trabalho](sql-data-warehouse-workload-importance.md) e [Classificação da carga de trabalho](sql-data-warehouse-workload-classification.md).
- Confira os artigos de instruções [Configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [Gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
