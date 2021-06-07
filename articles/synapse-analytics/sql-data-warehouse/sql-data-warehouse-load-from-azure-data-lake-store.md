---
title: Tutorial para carregar dados do Azure Data Lake Storage
description: Use a instrução de cópia para carregar dados de Azure Data Lake Storage para pools de SQL dedicados.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ca57c6200cf7006a89be4b1fd621974559e5b514
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606116"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Carregar dados de Azure Data Lake Storage em pools dedicados do SQL no Azure Synapse Analytics

Este guia descreve como usar a [instrução de cópia](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) para carregar dados de Azure data Lake Storage. Para obter exemplos rápidos sobre como usar a instrução de cópia em todos os métodos de autenticação, visite a seguinte documentação: [carregar dados com segurança usando pools de SQL dedicados](./quickstart-bulk-load-copy-tsql-examples.md).

> [!NOTE]  
> Para fornecer comentários ou relatar problemas na instrução de cópia, envie um email para a seguinte lista de distribuição: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Crie a tabela de destino para carregar dados de Azure Data Lake Storage.
> * Crie a instrução de cópia para carregar dados no data warehouse.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este tutorial, baixe e instale a versão mais recente do [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SQL Server Management Studio).

Para este tutorial, você precisa do:

* Um pool SQL dedicado. Consulte [criar um pool de SQL dedicado e dados de consulta](create-data-warehouse-portal.md).
* Uma conta do Data Lake Storage. Veja [Introdução ao Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Para essa conta de armazenamento, você precisará configurar ou especificar uma das seguintes credenciais a serem carregadas: uma chave de conta de armazenamento, chave de assinatura de acesso compartilhado (SAS), um usuário de aplicativo do Azure Directory ou um usuário do AAD que tem a função do Azure apropriada para a conta de armazenamento.

## <a name="create-the-target-table"></a>Criar a tabela de destino

Conecte-se ao seu pool SQL dedicado e crie a tabela de destino para a qual será carregada. Neste exemplo, estamos criando uma tabela de dimensões de produto.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>Criar a instrução de cópia

Conecte-se ao pool dedicado do SQL e execute a instrução de cópia. Para obter uma lista completa de exemplos, visite a seguinte documentação: [carregar dados com segurança usando pools de SQL dedicados](./quickstart-bulk-load-copy-tsql-examples.md).

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Otimizar a compactação columnstore

Por padrão, as tabelas são definidas como um índice columnstore clusterizado. Após a conclusão do carregamento, algumas das linhas de dados não podem ser compactadas no columnstore.  Há várias razões para isso acontecer. Para obter mais informações, confira [gerenciar índices columnstore](sql-data-warehouse-tables-index.md).

Para otimizar o desempenho da consulta e a compactação columnstore após um carregamento, recrie a tabela para forçar o índice columnstore a compactar todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar estatísticas

É melhor criar estatísticas de coluna única imediatamente após um carregamento. Há algumas opções de estatísticas. Por exemplo, se você criar estatísticas de coluna única em todas as colunas, poderá levar muito tempo para recompilar todas as estatísticas. Se você souber que determinadas colunas não estarão em predicados de consulta, você poderá ignorar a criação de estatísticas nessas colunas.

Se você decidir criar estatísticas com uma coluna em cada coluna de cada tabela, poderá usar o exemplo de código do procedimento armazenado `prc_sqldw_create_stats` no artigo [estatísticas](sql-data-warehouse-tables-statistics.md).

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Ele cria estatísticas de coluna única em cada coluna na tabela de dimensões e em cada coluna de junção das tabelas de fatos. Você poderá adicionar estatísticas de coluna única ou múltipla às colunas de tabelas de fatos posteriormente.

## <a name="achievement-unlocked"></a>Missão cumprida!

Você carregou com êxito os dados no data warehouse. Bom trabalho!

## <a name="next-steps"></a>Próximas etapas
Carregar dados é a primeira etapa para desenvolver uma solução de data warehouse usando o Azure Synapse Analytics. Confira nossos recursos de desenvolvimento.

> [!div class="nextstepaction"]
> [Saiba como desenvolver tabelas para o data warehouse](sql-data-warehouse-tables-overview.md)

Para obter mais exemplos de carregamento e referências, veja a seguinte documentação:
- [Documentação de referência de instrução de cópia](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [COPIAR exemplos para cada método de autenticação](./quickstart-bulk-load-copy-tsql-examples.md)
- [COPIAR guia de início rápido para uma única tabela](./quickstart-bulk-load-copy-tsql.md)