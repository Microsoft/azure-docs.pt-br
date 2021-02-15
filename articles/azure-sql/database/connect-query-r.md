---
title: Usar o R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia) para consultar um banco de dados
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra como usar um script R com os Serviços de Machine Learning do Banco de Dados SQL do Azure para se conectar a um Banco de Dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2e32a4abeae78aa7105f21ecffbb18c2eae841a4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185616"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Início Rápido: Usar o R com os Serviços de Machine Learning do Banco de Dados SQL do Azure (versão prévia) para consultar um banco de dados 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste início rápido, você usará o R com os Serviços de Machine Learning do Banco de Dados SQL do Azure para se conectar a um banco de dados no Banco de Dados SQL do Azure e usar instruções T-SQL para consultar os dados.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um [Banco de Dados SQL do Azure](single-database-create-quickstart.md)
- [Serviços do Machine Learning](../managed-instance/machine-learning-services-overview.md) com R habilitado.
- SSMS ([SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms))

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar o banco de dados do **Adventure Works**.

Os Serviços de Machine Learning com R são um recurso do Banco de Dados SQL do Azure, usado para executar scripts do R no banco de dados. Para obter mais informações, confira o [Projeto R](https://www.r-project.org/).

## <a name="get-the-sql-server-connection-information"></a>Obter as informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados no Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **Instâncias Gerenciadas de SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado ao lado do **Nome do servidor** para um banco de dados no Banco de Dados SQL do Azure ou o nome do servidor totalmente qualificado ao lado do **Host** para uma instância gerenciada na Instância Gerenciada de SQL do Azure. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

## <a name="create-code-to-query-your-database"></a>Criar um código para consultar o banco de dados

1. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados.

   Se você precisar de ajuda para se conectar, veja o [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um banco de dados no Banco de Dados SQL do Azure](connect-query-ssms.md).

1. Passar o script R completo para o procedimento armazenado [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   O script é passado por meio do argumento `@script`. Tudo dentro do argumento `@script` precisa ser código R válido.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se você receber erros, isso poderá ser devido à versão prévia pública dos Serviços de Machine Learning (com o R) não estar habilitada para o banco de dados. Veja os [Pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Execute o procedimento armazenado [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

1. Verifique se as primeiras 20 linhas de Categoria/Produto são retornadas na janela **Mensagens**.

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure](design-first-database-tutorial.md)
- [Serviços do Machine Learning do Banco de Dados SQL do Azure (com o R)](../managed-instance/machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context)