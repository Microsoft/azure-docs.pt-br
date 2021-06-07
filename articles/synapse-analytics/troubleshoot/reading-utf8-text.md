---
title: 'Solução de problemas: lendo texto UTF-8 de arquivos CSV ou PARQUET usando o pool SQL sem servidor'
description: Lendo texto UTF-8 de arquivos CSV ou PARQUET usando o pool SQL sem servidor no Azure Synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576410"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Solucionar problemas de leitura de texto UTF-8 de arquivos CSV ou parquet usando o pool SQL sem servidor no Azure Synapse Analytics

Este artigo fornece etapas de solução de problemas para ler texto UTF-8 de arquivos CSV ou parquet usando o pool SQL sem servidor no Azure Synapse Analytics.

Quando o texto UTF-8 for lido de um arquivo CSV ou PARQUET usando o pool SQL sem servidor, alguns caracteres especiais, como ü e ö, serão convertidos de forma incorreta se a consulta retornar colunas VARCHAR com agrupamentos não UTF8. Esse é um problema conhecido no SQL Server e no SQL do Azure. O agrupamento não UTF8 é o padrão no SQL Synapse para que as consultas de cliente sejam afetadas. Os clientes que usam caracteres do inglês padrão e alguns subconjuntos de caracteres latinos estendidos podem não notar os erros de conversão. A conversão incorreta é explicada com mais detalhes em [sempre usar agrupamentos UTF-8 para ler texto UTF-8 no pool SQL sem servidor](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Solução alternativa

A solução alternativa para esse problema é sempre usar o agrupamento UTF-8 ao ler texto UTF-8 de arquivos CSV ou PARQUET.

- Em muitos casos, você só precisa definir o agrupamento UTF8 no banco de dados (operação de metadados).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- Você pode definir explicitamente o agrupamento na coluna VARCHAR em OPENROWSET ou na tabela externa:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- Se você não especificou o agrupamento UTF8 em tabelas externas que lêem dados UTF8, será necessário recriar tabelas externas afetadas e definir o agrupamento UTF8 em colunas VARCHAR (operação de metadados).


## <a name="next-steps"></a>Próximas etapas

* [Consultar arquivos parquet com Synapse SQL](../sql/query-parquet-files.md)
* [Consultar arquivos CSV com Synapse SQL](../sql/query-single-csv-file.md)
* [CETAS com Synapse SQL](../sql/develop-tables-cetas.md)
* [Início Rápido: Usar o pool de SQL sem servidor](../quickstart-sql-on-demand.md)
