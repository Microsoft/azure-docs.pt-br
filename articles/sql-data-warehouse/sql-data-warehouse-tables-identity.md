---
title: Criar chaves substitutas usando IDENTITY | Microsoft Docs
description: Saiba como usar o IDENTITY para criar chaves substitutas em suas tabelas.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/13/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3ab5d159e6eaeb830135fe134e108b0e4de4b7d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Criar chaves substitutas usando IDENTITY
> [!div class="op_single_selector"]
> * [Visão geral][Overview]
> * [Tipos de dados][Data Types]
> * [Distribuir][Distribute]
> * [Índice][Index]
> * [Partição][Partition]
> * [Estatísticas][Statistics]
> * [Temporário][Temporary]
> * [Identidade][Identity]
> 
> 

Muitos modeladores de dados gostam de criar chaves substitutas em suas tabelas quando criam modelos de data warehouse. Você pode usar a propriedade IDENTITY para atingir esse objetivo de forma simples e eficiente, sem afetar o desempenho de carga. 

## <a name="get-started-with-identity"></a>Introdução ao IDENTITY
Você pode definir uma tabela como tendo a propriedade IDENTITY quando você cria a tabela pela primeira vez usando uma sintaxe semelhante à instrução a seguir:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Você pode usar `INSERT..SELECT` para popular a tabela.

## <a name="behavior"></a>Comportamento
A propriedade IDENTITY foi projetada para expansão em todas as distribuições no data warehouse sem afetar o desempenho de carga. Portanto, a implementação de IDENTITY é orientada para atingir esses objetivos. Esta seção destaca as nuances da implementação para ajudá-lo a entendê-los mais detalhadamente.  

### <a name="allocation-of-values"></a>Alocação de valores
A propriedade IDENTITY não garante a ordem na qual os valores substitutos são alocados, o que reflete o comportamento do SQL Server e do Banco de Dados SQL do Azure. No entanto, no SQL Data Warehouse do Azure, a ausência de uma garantia é mais pronunciada. 

O exemplo a seguir é uma ilustração:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

No exemplo anterior, duas linhas foram descarregadas na distribuição 1. A primeira linha tem o valor substituto de 1 na coluna `C1`, e a segunda linha tem o valor substituto 61. Ambos os valores foram gerados pela propriedade IDENTITY. No entanto, a alocação dos valores não é contígua. Este comportamento ocorre por design.

### <a name="skewed-data"></a>Dados distorcidos 
Os intervalos de valores para o tipo de dados são distribuídos igualmente pelas distribuições. Se uma tabela distribuída tiver dados distorcidos, então o intervalo de valores disponível para o tipo de dados pode ser esgotado prematuramente. Por exemplo, se todos os dados resultam em uma única distribuição, então efetivamente a tabela tem acesso a apenas um sexagésimo dos valores do tipo de dados. Por esse motivo, a propriedade de identidade é limitada somente aos tipos de dados `INT` e `BIGINT`.

### <a name="selectinto"></a>SELECT..INTO
Quando uma coluna de IDENTITY existente é selecionada em uma nova tabela, a nova coluna herda a propriedade IDENTITY, a menos que uma das seguintes condições seja verdadeira:
- A instrução SELECT contém uma junção.
- Várias instruções SELECT são unidas usando UNION.
- A coluna IDENTITY é listada mais de uma vez na lista SELECT.
- A coluna IDENTITY é parte de uma expressão.
    
Se qualquer uma das seguintes condições for verdadeira, a coluna é criada como NOT NULL em vez de herdar a propriedade IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) segue o mesmo comportamento do SQL Server que está documentado para SELECT..INTO. No entanto, você não pode especificar uma propriedade IDENTITY na definição de coluna da parte `CREATE TABLE` da instrução. Você também não pode usar a função IDENTITY na parte `SELECT` do CTAS. Para popular uma tabela, você precisa usar `CREATE TABLE` para definir a tabela, seguido de `INSERT..SELECT` para preenchê-la.

## <a name="explicitly-insert-values-into-an-identity-column"></a>Insera explicitamente os valores em uma coluna IDENTITY 
O SQL Data Warehouse oferece suporte à sintaxe `SET IDENTITY_INSERT <your table> ON|OFF`. Você pode usar essa sintaxe para inserir explicitamente os valores na coluna IDENTITY.

Muitos modeladores de dados gostam de usar valores negativos predefinidos para determinadas linhas em suas dimensões. Um exemplo é de -1 ou a linha "membro desconhecido". 

O próximo script mostra como adicionar essa linha explicitamente usando SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>Carregar dados em uma tabela com IDENTITY

A presença da propriedade IDENTITY tem algumas implicações no seu código de carregamento de dados. Esta seção destaca alguns padrões básicos para carregar dados em tabelas usando IDENTITY. 

### <a name="load-data-with-polybase"></a>Carregar dados com o PolyBase
Para carregar dados em uma tabela e gerar uma chave substituta, usando IDENTITY, crie a tabela e, em seguida, use INSERT..SELECT ou INSERT..VALUES para executar o carregamento.

O exemplo a seguir destaca o padrão básico:
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Não é possível usar `CREATE TABLE AS SELECT` atualmente ao carregar dados em uma tabela com uma coluna IDENTITY.
> 

Para obter mais informações sobre como carregar dados usando a ferramenta de programa de cópia em massa (BCP), consulte os seguintes artigos:

- [Carregar com PolyBase][]
- [Práticas recomendadas do PolyBase][]

### <a name="load-data-with-bcp"></a>Carregar dados com o BCP
O BCP é uma ferramenta de linha de comando que você pode usar para carregar dados no SQL Data Warehouse. Um dos seus parâmetros (-E) controla o comportamento do BCP ao carregar dados em uma tabela com uma coluna IDENTITY. 

Quando -E for especificado, os valores mantidos no arquivo de entrada para a coluna com IDENTITY serão retidos. Se -E *não* for especificado, os valores nesta coluna são ignorados. Se a coluna de identidade não for incluída, os dados são carregados normalmente. Os valores são gerados de acordo com a política de incremento e semente da propriedade.

Para obter mais informações sobre como carregar dados usando o BCP, consulte os seguintes artigos:

- [Carregar com BCP][]
- [BCP no MSDN][]

## <a name="catalog-views"></a>Exibições do catálogo
O SQL Data Warehouse oferece suporte à exibição do catálogo `sys.identity_columns`. Este modo de exibição pode ser usado para identificar uma coluna que tem a propriedade IDENTITY.

Para ajudá-lo a entender melhor o esquema de banco de dados, este exemplo mostra como integrar `sys.identity_columns` com outra exibições de catálogo do sistema:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitações
A propriedade IDENTITY não pode ser usada nos seguintes cenários:
- O tipo de dados da coluna não é INT ou BIGINT
- A coluna é também a chave de distribuição
- A tabela é uma tabela externa 

As funções relacionadas a seguir não têm suporte no SQL Data Warehouse:

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Tarefas

Esta seção fornece um código de exemplo que você pode usar para executar tarefas comuns ao trabalhar com colunas IDENTITY.

> [!NOTE] 
> A coluna C1 é a IDENTITY em todas as tarefas a seguir.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Localizar o maior valor alocado para uma tabela
Use a função `MAX()` para determinar o valor mais alto alocado para uma tabela distribuída:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Localizar a semente e o incremento para a propriedade IDENTITY
Você pode usar as exibições de catálogo para descobrir os valores de configuração da semente e do incremento da identidade para uma tabela usando a consulta a seguir: 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o desenvolvimento de tabelas, consulte [Visão geral da tabela][Overview], [Tipos de dados da tabela][Data Types], [Distribuição de uma tabela][Distribute], [Indexação de uma tabela][Index], [Partição de uma tabela][Partition] e [Tabelas temporárias][Temporary]. 
* Para saber mais sobre as práticas recomendadas, consulte [Práticas recomendadas do SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[Carregar com BCP]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[Carregar com PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[Práticas recomendadas do PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[BCP no MSDN]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
