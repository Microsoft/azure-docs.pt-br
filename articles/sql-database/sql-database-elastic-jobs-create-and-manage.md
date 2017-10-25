---
title: Gerenciar grupos dos bancos de dados SQL do Azure | Microsoft Docs
description: "Explore a criação e o gerenciamento de um trabalho elástico."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: f858344d-085b-4022-935e-1b5fa20adbac
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: d30cc74778e0b36dd632c2f040ce80d1ca8af5a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Criar e gerenciar Bancos de Dados SQL do Azure com escala horizontal usando trabalhos elásticos (preview)


**Trabalhos de Banco de Dados Elástico** simplificam o gerenciamento de grupos de bancos de dados executando operações administrativas como alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, coleta dados de desempenho ou de telemetria do locatário (cliente). Trabalhos de Banco de Dados Elástico está disponível atualmente por meio de cmdlets do PowerShell e do Portal do Azure. No entanto, o portal do Azure revela funcionalidade reduzida limitada à execução em todos os bancos de dados em um [pool elástico (visualização)](sql-database-elastic-pool.md). Para acessar recursos adicionais e execução de scripts em um grupo de bancos de dados, incluindo uma coleção definida de modo personalizado ou um conjunto de fragmentos (criado usando a [biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-scale-introduction.md)), consulte [Criando e gerenciando trabalhos usando o PowerShell](sql-database-elastic-jobs-powershell.md). Para saber mais, confira [Visão geral de trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Para obter uma avaliação gratuita, veja [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um pool elástico. Consulte [Sobre pools elásticos](sql-database-elastic-pool.md).
* Instalação dos componentes de serviço do trabalho de banco de dados elástico. Consulte [Instalando o serviço do trabalho de banco de dados elástico](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Criando trabalhos
1. Usando o [Portal do Azure](https://portal.azure.com), de um pool de trabalho de banco de dados elástico existente, clique em **Criar trabalho**.
2. Digite o nome de usuário e a senha do administrador do banco de dados (criado na instalação dos Trabalhos) para o banco de dados de controle de trabalhos (armazenamento de metadados para trabalhos).
   
    ![Nomeie o trabalho, digite ou cole o código e clique em Executar][1]
3. Na folha **Criar trabalho** , digite um nome para o trabalho.
4. Digite o nome de usuário e a senha para se conectar aos bancos de dados de destino com permissões suficientes para que a execução do script seja bem-sucedida.
5. Cole ou digite o script T-SQL.
6. Clique em **Salvar** e em **Executar**.
   
    ![Criar trabalhos e executar][5]

## <a name="run-idempotent-jobs"></a>Executar trabalhos idempotentes
Quando você executa um script em um conjunto de bancos de dados, certifique-se de que o script seja idempotente. Ou seja, o script deve ser capaz de executar várias vezes, mesmo se tiver falhado antes em um estado incompleto. Por exemplo, quando um script falha, o trabalho é repetido automaticamente até obter êxito (dentro dos limites, uma vez que lógica de nova tentativa eventualmente deixará de tentar novamente). A maneira de fazer isso é usar a cláusula "IF EXISTS" e excluir qualquer instância encontrada antes de criar um novo objeto. Veja um exemplo aqui:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Como alternativa, use uma cláusula "IF NOT EXISTS" antes de criar uma nova instância:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Em seguida, esse script atualiza a tabela criada anteriormente.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Verificando o status do trabalho
Após o início de um trabalho, você pode verificar seu andamento.

1. Na página do pool elástico, clique em **Gerenciar trabalhos**.
   
    ![Clique em “Gerenciar trabalhos”][2]
2. Clique no nome (a) de um trabalho. O **STATUS** pode ser "Concluído" ou "Falha". Os detalhes do trabalho aparecem (b) com sua data e hora de criação e execução. A lista (c) abaixo mostra o progresso do script em cada banco de dados no pool, oferecendo detalhes de data e hora.
   
    ![Verificando um trabalho concluído][3]

## <a name="checking-failed-jobs"></a>Verificação de trabalhos com falha
Se um trabalho falhar, será possível encontrar um log de sua execução. Clique no nome do trabalho com falha para ver seus detalhes.

![Verificar um trabalho com falha][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


