---
title: Melhores práticas do pool de SQL sem servidor
description: Recomendações e práticas recomendadas para trabalhar com o pool SQL sem servidor.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a47982012dcaa2eabda93c93508b23f30525812d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720382"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Práticas recomendadas para o pool SQL sem servidor no Azure Synapse Analytics

Neste artigo, você encontrará uma coleção de práticas recomendadas para usar o pool SQL sem servidor. O pool SQL sem servidor é um recurso no Azure Synapse Analytics.

O pool SQL sem servidor permite consultar arquivos em suas contas de armazenamento do Azure. Ele não tem armazenamento local nem recursos de ingestão. Portanto, todos os arquivos que a consulta tem como destino são externos ao pool SQL sem servidor. Tudo relacionado à leitura de arquivos do armazenamento pode ter um impacto no desempenho da consulta.

## <a name="client-applications-and-network-connections"></a>Aplicativos cliente e conexões de rede

Verifique se o aplicativo cliente está conectado ao espaço de trabalho Synapse mais próximo possível com a conexão ideal.
- Coloque um aplicativo cliente com o espaço de trabalho Synapse. Se você estiver usando aplicativos como Power BI ou o Azure Analysis Service, verifique se eles estão na mesma região em que você colocou seu espaço de trabalho Synapse. Se necessário, crie os espaços de trabalho separados que são emparelhados com seus aplicativos cliente. Colocar um aplicativo cliente e o espaço de trabalho Synapse em uma região diferente poderia causar maior latência e streaming mais lento de resultados.
- Se você estiver lendo dados de seu aplicativo local, verifique se o espaço de trabalho Synapse está na região que está perto do seu local.
- Verifique se você não tem alguns problemas de largura de banda de rede ao ler uma grande quantidade de dados.
- Não use o Synapse Studio para retornar uma grande quantidade de dados. O Synapse Studio é uma ferramenta da Web que usa o protocolo HTTPS para transferir dados. Use Azure Data Studio ou SQL Server Management Studio para ler uma grande quantidade de dados.

## <a name="storage-and-content-layout"></a>Armazenamento e layout de conteúdo

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Coloque seu pool SQL sem servidor e de armazenamento

Para minimizar a latência, coloque a conta de armazenamento do Azure ou o armazenamento analítico CosmosDB e o ponto de extremidade do pool SQL sem servidor. As contas de armazenamento e os pontos de extremidade provisionados durante a criação do workspace estão localizados na mesma região.

Para obter um desempenho ideal, se você acessar outras contas de armazenamento com o pool SQL sem servidor, verifique se elas estão na mesma região. Se elas não estiverem na mesma região, haverá uma latência maior para a transferência de rede dos dados entre a região remota e as regiões do ponto de extremidade.

### <a name="azure-storage-throttling"></a>Limitação do Armazenamento do Azure

Vários aplicativos e serviços podem acessar sua conta de armazenamento. A limitação de armazenamento ocorre quando o IOPS combinado ou a taxa de transferência gerada por aplicativos, serviços e carga de trabalho do pool SQL sem servidor excedem os limites da conta de armazenamento. Como resultado, você enfrentará um impacto negativo significativo no desempenho da consulta.

Quando a limitação é detectada, o pool do SQL sem servidor tem tratamento interno para resolvê-lo. O pool SQL sem servidor fará solicitações de armazenamento em um ritmo mais lento até que a limitação seja resolvida.

> [!TIP]
> Para uma execução de consulta ideal, não sobrecarregue a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

### <a name="azure-ad-pass-through-performance"></a>Desempenho de passagem do Azure AD

O pool SQL sem servidor permite que você acesse arquivos no armazenamento usando as credenciais de passagem do Azure Active Directory (Azure AD) ou SAS. Você pode enfrentar um desempenho mais lento com a passagem em comparação à SAS do Azure AD.

Se você precisar de um melhor desempenho, tente usar credenciais SAS para acessar o armazenamento.

### <a name="prepare-files-for-querying"></a>Preparar arquivos para consulta

Se possível, você pode preparar arquivos para melhorar o desempenho:

- Converta CSV grande e JSON em parquet. Parquet é um formato de coluna. Como ele é compactado, os tamanhos de arquivo são menores do que arquivos CSV ou JSON com os mesmos dados. O pool SQL sem servidor é capaz de ignorar as colunas e as linhas que não são necessárias na consulta se você estiver lendo arquivos parquet. O pool SQL sem servidor precisará de menos tempo e menos solicitações de armazenamento para lê-lo.
- Se uma consulta tiver como objetivo um único arquivo grande, dividi-lo em vários arquivos menores será vantajoso para você.
- Tente manter o tamanho do arquivo CSV entre 100 MB e 10 GB.
- É melhor ter arquivos com o mesmo tamanho para um único caminho OPENROWSET ou um local de tabela externa.
- Particione os dados armazenando partições em diferentes pastas ou nomes de arquivos. Consulte [Usar as funções fileinfo e filepath para segmentar partições específicas](#use-filename-and-filepath-functions-to-target-specific-partitions).

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Coloque o armazenamento analítico do CosmosDB e o pool SQL sem servidor

Certifique-se de que o armazenamento analítico CosmosDB seja colocado na mesma região que o espaço de trabalho Synapse. As consultas entre regiões podem causar grandes latências.

## <a name="csv-optimizations"></a>Otimizações de CSV

### <a name="use-parser_version-20-to-query-csv-files"></a>Usar PARSER_VERSION 2.0 para consultar arquivos CSV

Você pode usar um analisador otimizado para desempenho ao consultar arquivos CSV. Para obter detalhes, consulte [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Criar manualmente estatísticas para arquivos CSV

O pool SQL sem servidor se baseia em estatísticas para gerar planos de execução de consulta ideais. As estatísticas serão criadas automaticamente para colunas em arquivos parquet quando necessário. Neste momento, as estatísticas não são criadas automaticamente para colunas em arquivos CSV e você deve criar estatísticas manualmente para as colunas que você usa em consultas, particularmente as usadas em DISTINCT, junção, WHERE, ORDENAr por e agrupar por. Verifique as [estatísticas no pool SQL sem servidor](develop-tables-statistics.md#statistics-in-serverless-sql-pool) para obter detalhes.


## <a name="data-types"></a>Tipos de dados

### <a name="use-appropriate-data-types"></a>Usar tipos de dados apropriados

Os tipos de dados usados na consulta afetam o desempenho. Obtenha um melhor desempenho segundo as instruções a seguir: 

- Usar o menor tamanho de dados que acomodará o maior valor possível.
  - Se o comprimento máximo do valor de caracteres for de 30 caracteres, use um tipo de dados de caractere de comprimento 30.
  - Se todos os valores de coluna de caracteres forem de tamanho fixo, use **char** ou **nchar**. Caso contrário, use **varchar** ou **nvarchar**.
  - Se o valor máximo inteiro da coluna for 500, use **smallint**, pois é o menor tipo de dados que pode acomodar esse valor. Encontre os intervalos de tipo de dados inteiro [neste artigo](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Se possível, use **varchar** e **char** em vez de **nvarchar** e **nchar**.
- Use tipos de dados baseados em inteiro, se possível. As operações SORT, JOIN e GROUP são concluídas mais rapidamente em dados do tipo inteiro do que em dados de caracteres.
- Se você estiver usando a inferência de esquema, [verifique os tipos de dados inferidos](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Verificar tipos de dados inferidos

A [inferência de esquema](query-parquet-files.md#automatic-schema-inference) ajuda a escrever rapidamente consultas e explorar dados sem conhecer os esquemas de arquivos. O custo dessa conveniência é que os tipos de dados inferidos podem ser maiores que os tipos de dados reais. Isso acontece quando não há informações suficientes nos arquivos de origem para garantir que o tipo de dados apropriado seja usado. Por exemplo, os arquivos Parquet não contêm metadados sobre o comprimento máximo da coluna de caracteres. Portanto, o pool SQL sem servidor infere como varchar (8000).

Use [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) para verificar os tipos de dados resultantes da consulta.

O exemplo a seguir mostra como você pode otimizar os tipos de dados inferidos. Esse procedimento é usado para mostrar os tipos de dados inferidos: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Este é o conjunto de resultados:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|Datetime2 (7)|8|
|0|3|passenger_count|INT|4|

Depois de conhecermos os tipos de dados inferidos para a consulta, podemos especificar os tipos de dados apropriados:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Otimização de filtro

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Enviar curingas por push para níveis inferiores no caminho

Você pode usar curingas no seu caminho para [consultar vários arquivos e pastas](query-data-storage.md#query-multiple-files-or-folders). O pool SQL sem servidor lista os arquivos em sua conta de armazenamento, começando no primeiro * usando a API de armazenamento. Ele elimina os arquivos que não correspondem ao caminho especificado. A redução da lista de arquivos inicial pode melhorar o desempenho se houver muitos arquivos que correspondam ao caminho especificado até o primeiro caractere curinga.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Usar as funções fileinfo e filepath para segmentar partições específicas

Os dados geralmente são organizados em partições. Você pode instruir o pool SQL sem servidor a consultar pastas e arquivos específicos. Fazer isso reduzirá o número de arquivos e a quantidade de dados que a consulta precisa ler e processar. Um bônus adicional é que você obterá um melhor desempenho.

Para obter mais informações, leia sobre as funções [filename](query-data-storage.md#filename-function) e [filepath](query-data-storage.md#filepath-function) e veja os exemplos para [consultar arquivos específicos](query-specific-files.md).

> [!TIP]
> Sempre converta os resultados das funções filepath e filename nos tipos de dados apropriados. Se você usar os tipos de dados de caracteres, certifique-se de usar o comprimento apropriado.

> [!NOTE]
> Atualmente, não há suporte para funções usadas para eliminação de partição, filepath e filename, em tabelas externas diferentes daquelas criadas automaticamente para cada tabela criada no Apache Spark do Azure Synapse Analytics.

Se os dados armazenados não estiverem particionados, considere particioná-los. Dessa forma, você pode usar essas funções para otimizar consultas direcionadas a esses arquivos. Quando você [consulta Apache Spark particionado para tabelas Synapse do Azure](develop-storage-files-spark-tables.md) do pool SQL sem servidor, a consulta automaticamente direcionará somente os arquivos necessários.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Usar agrupamento adequado para utilizar a aplicação de predicado para colunas de caracteres

Os dados no arquivo parquet são organizados em grupos de linhas. O pool SQL sem servidor ignora grupos de linhas com base no predicado especificado na cláusula WHERE e, portanto, reduz a e/s, o que resulta em maior desempenho de consulta. 

Observe que a aplicação de predicado para colunas de caracteres em arquivos parquet tem suporte somente para agrupamento Latin1_General_100_BIN2_UTF8. Você pode especificar o agrupamento para uma coluna específica usando a cláusula WITH. Se você não especificar esse agrupamento usando a cláusula WITH, o agrupamento de banco de dados será usado.

## <a name="optimize-repeating-queries"></a>Otimizar consultas repetitivas

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para aprimorar o desempenho e as junções de consulta

O [CETAS](develop-tables-cetas.md) é um dos recursos mais importantes disponíveis no pool SQL sem servidor. CETAS é uma operação paralela que cria metadados de tabela externa e exporta os resultados da consulta SELECT para um conjunto de arquivos em sua conta de armazenamento.

Você pode usar o CETAS para materializar partes usadas com frequência de consultas, como tabelas de referência Unidas, para um novo conjunto de arquivos. Você pode unir a essa única tabela externa em vez de repetir junções comuns em várias consultas.

Como CETAS gera arquivos parquet, as estatísticas serão criadas automaticamente quando a primeira consulta for direcionada a essa tabela externa, resultando em desempenho aprimorado para consultas subsequentes direcionadas à tabela gerada com CETAS.

## <a name="next-steps"></a>Próximas etapas

Veja o artigo de [solução de problemas](resources-self-help-sql-on-demand.md) para conhecer os problemas e as soluções comuns. Se você estiver trabalhando com um pool SQL dedicado, em vez de pool SQL sem servidor, consulte [práticas recomendadas para pools de SQL dedicados](best-practices-dedicated-sql-pool.md) para obter diretrizes específicas.
