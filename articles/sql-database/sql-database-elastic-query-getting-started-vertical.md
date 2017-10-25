---
title: "Introdução às consultas entre bancos de dados (particionamento vertical) | Microsoft Docs"
description: "como usar a consulta de banco de dados elástico com bancos de dados particionados verticalmente"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: e5b44b10-c432-4f96-b20e-08615ff4d5dd
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: torsteng
ms.openlocfilehash: 17158c4960e9ba9251524659c90af9aec1316774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Introdução às consultas entre bancos de dados (particionamento vertical) (preview)
A consulta de banco de dados elástico (visualização) para o Banco de Dados SQL do Azure permite executar consultas T-SQL que abrangem vários bancos de dados usando um único ponto de conexão. Este tópico se aplica a [bancos de dados particionados verticalmente](sql-database-elastic-query-vertical-partitioning.md).  

Quando tiver concluído, você saberá como configurar e usar um Banco de Dados SQL do Azure para executar consultas que abrangem vários bancos de dados relacionados. 

Para saber mais sobre o recurso de consulta de banco de dados elástico, veja a [visão geral da consulta de banco de dados elástico do Banco de Dados SQL do Azure](sql-database-elastic-query-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter a permissão para ALTERAR QUALQUER FONTE DE DADOS EXTERNA. Essa permissão está incluída na permissão ALTERAR BANCO DE DADOS. As permissões para ALTERAR QUALQUER FONTE DE DADOS EXTERNA são necessárias para referenciar a fonte de dados subjacente.

## <a name="create-the-sample-databases"></a>Criar os bancos de dados de exemplo
Para começar, precisamos criar dois bancos de dados, **Customers** e **Orders**, nos mesmos servidores lógicos ou em servidores diferentes.   

Execute as consultas a seguir no banco de dados **Orders** para criar a tabela **OrderInformation** e inserir os dados de exemplo. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Agora, execute a consulta a seguir no banco de dados **Customers** para criar a tabela **CustomerInformation** e inserir os dados de exemplo. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Criar objetos de banco de dados
### <a name="database-scoped-master-key-and-credentials"></a>Chave mestra e credenciais do escopo do banco de dados
1. Abra o SQL Server Management Studio ou o SQL Server Data Tools no Visual Studio.
2. Conecte-se ao banco de dados Orders e execute os seguintes comandos T-SQL:
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    O "nome de usuário" e a "senha" devem ser o nome de usuário e a senha usados para fazer logon no banco de dados Customers.
    Atualmente não há suporte para a autenticação usando o Azure Active Directory com consultas elásticas.

### <a name="external-data-sources"></a>Fontes de dados externas
Para criar uma fonte de dados externa, execute o seguinte comando no banco de dados Orders: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa ao banco de dados Orders, que corresponde à definição da tabela CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta T-SQL no banco de dados elástico de exemplo
Depois de definir sua fonte de dados e suas tabelas externas, agora você poderá usar o T-SQL para consultar suas tabelas externas. Execute esta consulta no banco de dados Orders: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Custo
Atualmente, o recurso de consulta de banco de dados elástico está incluído no custo de seu Banco de Dados SQL do Azure.  

Para saber mais sobre preços, consulte [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database). 

## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral de consulta elástica, consulte [Visão geral de consulta elástica](sql-database-elastic-query-overview.md).
* Para sintaxe e amostras de consultas para dados particionados verticalmente, consulte [Consultando dados particionados verticalmente)](sql-database-elastic-query-vertical-partitioning.md)
* Para um tutorial sobre particionamento horizontal (fragmentação), consulte [Introdução à consulta elástica para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).
* Para sintaxe e amostras de consultas para dados particionados horizontalmente, consulte [Consultando dados particionados horizontalmente)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução Transact-SQL em um único Banco de Dados SQL do Azure remoto ou um conjunto de bancos de dados que serve como fragmentos em um esquema de particionamento horizontal.