---
title: 'Início Rápido: Criar um classificador de carga de trabalho – T-SQL | Microsoft Docs'
description: Use o T-SQL para criar um classificador de carga de trabalho com alta importância.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: f400989fdbdede4f4a07ee13c5a606d51529150c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588696"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Início Rápido: Criar um classificador de carga de trabalho usando o T-SQL

Neste início rápido, você criará rapidamente um classificador de carga de trabalho com alta importância para o CEO da sua organização. Esse classificador de carga de trabalho permitirá que as consultas do CEO tenham precedência sobre outras consultas com menos importância na fila.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pressupõe que você já tem um SQL Data Warehouse e que você tenha permissões CONTROL DATABASE. Se precisar, use [Criar e conectar – portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Criar logon para TheCEO

Crie um logon de autenticação do SQL Server no banco de dados `master` usando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) para “TheCEO”.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar usuário

[Criar usuário](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO", no mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Criar um [classificador de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) para "TheCEO" com alta importância.

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

## <a name="clean-up-resources"></a>Limpar recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Você está sendo cobrado por unidades de data warehouse e pelos dados armazenados em seu data warehouse. Esses recursos de computação e armazenamento são cobrados separadamente.

- Se desejar manter os dados no armazenamento, será possível pausar a computação quando você não estiver usando o data warehouse. Ao pausar a computação, você será cobrado apenas pelo armazenamento de dados. Quando você estiver pronto para trabalhar com os dados, retome a computação.
- Se desejar remover encargos futuros, será possível excluir o data warehouse.

Siga estas etapas para limpar os recursos.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione seu data warehouse.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para pausar a computação, selecione o botão **Pausar**. Quando o data warehouse for pausado, você verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o data warehouse para não ser cobrado pela computação ou pelo armazenamento, selecione **Excluir**.

4. Para remover o SQL Server criado, selecione **mynewserver-20180430.database.windows.net** na imagem anterior e, em seguida, selecione **Excluir**.  Tenha cuidado com essa exclusão, uma vez que a exclusão do servidor também exclui todos os bancos de dados atribuídos ao servidor.

5. Para remover o grupo de recursos, selecione **myResourceGroup** e, em seguida, **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

- Agora você criou um classificador de carga de trabalho. Execute algumas consultas como TheCEO para ver o desempenho delas. Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir consultas e a importância atribuída.
- Para saber mais sobre o gerenciamento de carga de trabalho do SQL Data Warehouse do Azure, confira [Importância da carga de trabalho](sql-data-warehouse-workload-importance.md) e [Classificação da carga de trabalho](sql-data-warehouse-workload-classification.md).
- Confira os artigos de instruções [Configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [Gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
