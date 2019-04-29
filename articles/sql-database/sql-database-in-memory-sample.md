---
title: Exemplo de In-Memory do Banco de Dados SQL do Azure | Microsoft Docs
description: Experimente o Banco de Dados SQL do Azure de tecnologias In-Memory com OLTP e exemplo de columnstore.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 2aa98c3958f1dffeb8adbad5e91a11f397d4a9fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035700"
---
# <a name="in-memory-sample"></a>Exemplo de In-Memory

As tecnologias in-Memory no Banco de Dados SQL do Azure que podem melhorar o desempenho do seu aplicativo e reduzir potencialmente o custo do banco de dados. Ao usar tecnologias In-Memory no Banco de Dados SQL do Azure, obtenha melhorias de desempenho com várias cargas de trabalho.

Neste artigo, você verá dois exemplos que ilustram o uso do OLTP In-Memory, bem como dos índices columnstore, no Banco de Dados SQL do Azure.

Para obter mais informações, consulte:
- [Visão geral e cenários de uso do OLTP In-Memory](https://msdn.microsoft.com/library/mt774593.aspx) (incluindo referências a estudos de caso de cliente e informações para começar)
- [Documentação para OLTP in-memory](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guia de índices ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)
- HTAP (Processamento Transacional e Analítico Híbrido), também conhecido como [análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instalar o exemplo de OLTP Na Memória.

Você pode criar o banco de dados de exemplo AdventureWorksLT com alguns cliques no [Portal do Azure](https://portal.azure.com/). Em seguida, as etapas desta seção explicam como você pode aprimorar seu banco de dados AdventureWorksLT com objetos OLTP in-memory e demonstram os benefícios de desempenho.

Para ver uma demonstração de desempenho mais simples, porém, mais visualmente interessante do OLTP in-memory, veja:

- Versão: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Código-fonte: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Etapas de instalação

1. No [portal do Azure](https://portal.azure.com/), crie um banco de dados Premium ou Comercialmente Crítico em um servidor. Defina a **Origem** como o banco de dados de exemplo AdventureWorksLT. Para obter instruções detalhadas, consulte [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-single-database-get-started.md).

2. Conecte-se ao banco de dados com o SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Copie o [script Transact-SQL do OLTP Na Memória](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) para a área de transferência. O script T-SQL cria os objetos necessários In-Memory no banco de dados de exemplo AdventureWorksLT criado na etapa 1.

4. Cole o script T-SQL no SSMS e execute o script. As instruções CREATE TABLE da cláusula `MEMORY_OPTIMIZED = ON` são cruciais. Por exemplo:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erro 40536


Se você receber o erro 40536 quando executar o script T-SQL, execute o seguinte script T-SQL para verificar se o banco de dados oferece suporte a Na Memória:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Um resultado **0** significa que não há suporte para In-Memory e **1** significa que há suporte. Para diagnosticar o problema, verifique se o banco de dados está na camada de serviço Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sobre os itens criados com otimização de memória

**Tabelas**: O exemplo contém as seguintes tabelas com otimização de memória:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Você pode inspecionar as tabelas com otimização de memória por meio do **Pesquisador de Objetos** no SSMS. Clique com o botão direito do mouse em **Tabelas** > **Filtro** > **Configurações do Filtro** > **Com otimização de memória**. O valor é igual a 1.


Ou então, você pode consultar as exibições do catálogo, tal como:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimentos armazenados compilados nativamente**: Você pode inspecionar SalesLT.usp_InsertSalesOrder_inmem por meio de uma consulta de exibição de catálogo:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Executar a carga de trabalho OLTP

A única diferença entre os dois *procedimentos armazenados* a seguir é que o primeiro procedimento usa versões com otimização de memória das tabelas, enquanto o segundo procedimento usa as tabelas em disco regulares:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


Nesta seção, você verá como usar o utilitário **ostress.exe** para executar os dois procedimentos armazenados em níveis estressantes. Você pode comparar quanto tempo as duas execuções demoram para serem concluídas.


Quando executar ostress.exe, recomendamos será passar valores de parâmetro projetados para ambos os seguintes:

- Execute um grande número de conexões simultâneas usando -n100.
- Faça com que cada conexão entre em loop centenas de vezes usando -r500.


No entanto, talvez você queira começar com valores muito menores, como -n10 e -r50 para garantir que tudo esteja funcionando.


### <a name="script-for-ostressexe"></a>Script para ostress.exe


Esta seção exibe o script T-SQL, que está inserido em nossa linha de comando do ostress.exe. O script usa itens que foram criados pelo script T-SQL instalado anteriormente.


O script a seguir insere um pedido de vendas de exemplo com cinco itens de linha nas seguintes *tabelas*com otimização de memória:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Para criar a versão *_ondisk* do script T-SQL anterior para ostress.exe, substitua as duas ocorrências da subcadeia de caracteres *_inmem* por *_ondisk*. Essas substituições afetam os nomes de tabelas e os procedimentos armazenados.


### <a name="install-rml-utilities-and-ostress"></a>Instalar utilitários RML e `ostress`


O ideal é você planejar executar o ostress.exe em uma VM (máquina virtual) do Azure. Você criaria uma [VM do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica do Azure em que seu banco de dados AdventureWorksLT reside. Mas você pode executar o ostress.exe em seu laptop.


Na VM ou em qualquer host que você escolher, instale os utilitários RML (Replay Markup Language). Os utilitários incluem ostress.exe.

Para obter mais informações, consulte:
- A discussão sobre ostress.exe no [Banco de dados de exemplo para OLTP In-Memory](https://msdn.microsoft.com/library/mt465764.aspx).
- [Banco de dados de exemplo para OLTP In-Memory](https://msdn.microsoft.com/library/mt465764.aspx).
- O [blog para instalar o ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Executar a carga de trabalho de estresse do *_inmem* primeiro


Você pode usar uma janela *Prompt Cmd RML* para executar nossa linha de comando do ostress.exe. Os parâmetros de linha de comando direcionam o `ostress` para:

- Execute 100 conexões simultaneamente (-n100).
- Faça cada conexão executar o script T-SQL 50 vezes (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comando do ostress.exe anterior:


1. Redefina o conteúdo de dados do banco de dados executando o seguinte comando no SSMS para excluir todos os dados inseridos por todas as execuções anteriores:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copie o texto da linha de comando anterior do ostress.exe para a área de transferência.

3. Substitua o `<placeholders>` para os parâmetros -S -U -P -d pelos valores reais corretos.

4. Execute a linha de comando editada em uma janela Cmd RML.


#### <a name="result-is-a-duration"></a>O resultado é uma duração


Quando o `ostress.exe` é concluído, ele grava a duração da execução como sua linha final de saída na janela Cmd RML. Por exemplo, uma execução de teste mais curta dura aproximadamente 1,5 minuto:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Redefinir, editar *_ondisk* e executar novamente


Depois de obter o resultado da execução do *_inmem*, realize as seguintes etapas para a execução de *_ondisk*:


1. Redefina o banco de dados executando o seguinte comando no SSMS para excluir todos os dados inseridos pela execução anterior:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Edite a linha de comando do ostress.exe para substituir todos os *_inmem* por *_ondisk*.

3. Execute novamente o ostress.exe pela segunda vez e capture o resultado da duração.

4. Redefina novamente o banco de dados (para exclusão responsável do que pode ser uma grande quantidade de dados de teste).


#### <a name="expected-comparison-results"></a>Resultados esperados para a comparação

Os testes In-Memory mostraram uma melhoria de desempenho de **nove vezes** para essa carga de trabalho simplista, com o `ostress` sendo executado em uma VM do Azure na mesma região do Azure que o banco de dados.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instalar o exemplo de Análise Na Memória


Nesta seção, você vai comparar os resultados de E/S e de estatísticas ao usar um índice columnstore versus um índice b-tree tradicional.


Para fazer uma análise em tempo real em uma carga de trabalho OLTP, quase sempre será melhor usar um índice columnstore não clusterizado. Para ver mais detalhes, confira [Índices Columnstore Descritos](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise de columnstore


1. Use o portal do Azure para criar um novo banco de dados AdventureWorksLT desde o exemplo.
   - Use esse nome exato.
   - Escolha qualquer camada de serviço Premium.

2. Copie o [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) para sua área de transferência.
   - O script T-SQL cria os objetos necessários In-Memory no banco de dados de exemplo AdventureWorksLT criado na etapa 1.
   - O script cria a tabela Dimension e duas tabelas de fatos. As tabelas de fatos são preenchidas com 3,5 milhões de linhas cada.
   - O script pode levar 15 minutos para ser concluído.

3. Cole o script T-SQL no SSMS e execute o script. A palavra-chave **COLUMNSTORE** na instrução **CREATE INDEX** é crucial, como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Defina o AdventureWorksLT com um nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    O nível 130 não está diretamente relacionado aos recursos Na Memória. Mas o nível 130 geralmente oferece desempenho de consulta mais rápido que o 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tabelas chave e índices de columnstore


- dbo.FactResellerSalesXL_CCI é uma tabela com um índice columnstore clusterizado, que tem compactação avançada no nível de *dados*.

- dbo.FactResellerSalesXL_PageCompressed é uma tabela com um índice clusterizado regular equivalente, compactado somente no nível de *página*.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Consultas chave para comparar o índice columnstore


Há [diversos tipos de consulta T-SQL que podem ser executados](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) para ver as melhorias de desempenho. Na etapa 2 no script T-SQL, preste atenção neste par de consultas. Elas diferem apenas em uma linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Um índice columnstore clusterizado está na tabela FactResellerSalesXL\_CCI.

O seguinte trecho de script T-SQL imprime estatísticas de E/S e de TIME para a consulta de cada tabela.


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

Em um banco de dados com o tipo de preço P2, você pode esperar um ganho de desempenho de cerca de nove vezes para essa consulta usando o índice columnstore clusterizado em comparação com o índice tradicional. Com P15, você pode esperar cerca de 57 vezes o ganho de desempenho ao usar o índice columnstore.



## <a name="next-steps"></a>Próximas etapas

- [Início Rápido 1: Tecnologias OLTP In-Memory para um desempenho mais rápido do T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Usar o OLTP In-Memory em um aplicativo existente do SQL Azure](sql-database-in-memory-oltp-migration.md)

- [Monitorar o armazenamento do OLTP In-Memory](sql-database-in-memory-oltp-monitoring.md) para o OLTP In-Memory


## <a name="additional-resources"></a>Recursos adicionais

#### <a name="deeper-information"></a>Informações mais detalhadas

- [Saiba como o Quorum dobra a principal carga de trabalho do banco de dados, enquanto reduz a DTU em 70% com o OLTP in-memory no Banco de Dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Postagem de Blog de OLTP na memória do Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Saiba mais sobre o OLTP in-memory](https://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre os índices columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Saiba mais sobre a análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

- Veja [Padrões comuns de carga de trabalho e considerações sobre migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve os padrões de carga de trabalho para os quais o OLTP In-Memory geralmente fornece ganhos significativos de desempenho)

#### <a name="application-design"></a>Design do aplicativo

- [OLTP Na Memória (Otimização Na Memória)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Usar o OLTP In-Memory em um aplicativo existente do SQL Azure](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SSDT (Ferramentas de Dados do SQL Server)](https://msdn.microsoft.com/library/mt204009.aspx)
