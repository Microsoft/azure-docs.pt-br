---
title: Consultar dados em Azure Data Lake usando o Azure Data Explorer
description: Saiba como consultar dados em Azure Data Lake usando o Data Explorer do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: cd53e1386d9d6f2a38beb1661554c8cc9116169d
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494857"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Consultar dados em Azure Data Lake usando o Data Explorer do Azure (versão prévia)

Azure Data Lake Storage é uma solução data Lake altamente escalonável e econômica para análise de Big Data. Ele combina o poder de um sistema de arquivos de alto desempenho com grande escala e economia para ajudá-lo a acelerar seu tempo de insights. O Data Lake Store Gen2 estende as funcionalidades do Armazenamento de Blobs do Azure e é otimizado para cargas de trabalho de análise.
 
O Azure Data Explorer integra-se com o armazenamento de BLOBs do Azure e Azure Data Lake Storage Gen2, fornecendo acesso rápido, armazenado em cache e indexado aos dados no Lake. Você pode analisar e consultar dados no Lake sem ingestão anterior no Azure Data Explorer. Você também pode consultar dados nativos do Lake ingeridos e indesejados simultaneamente.  

> [!TIP]
> O melhor desempenho de consulta exige a ingestão de dados no Azure Data Explorer. A capacidade de consultar dados em Azure Data Lake Storage Gen2 sem ingestão anterior deve ser usada apenas para dados históricos ou dados que raramente são consultados.
 
## <a name="optimize-query-performance-in-the-lake"></a>Otimizar o desempenho de consulta no Lake 

* Particione os dados para melhorar o desempenho e o tempo de consulta otimizado.
* Compacte dados para melhorar o desempenho (gzip para melhor compactação, lz4 para melhor desempenho).
* Use o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2 com a mesma região que o cluster de Data Explorer do Azure. 

## <a name="create-an-external-table"></a>Criar uma tabela externa

 > [!NOTE]
 > Atualmente, as contas de armazenamento com suporte são o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2. Atualmente, os formatos de dados com suporte são JSON, CSV, TSV e txt.

1. Use o `.create external table` comando para criar uma tabela externa no Azure data Explorer. Comandos de tabela externa adicionais, `.show`como `.drop`, e `.alter` são documentados em [comandos de tabela externa](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    Esta consulta cria partições diárias *Container1/aaaa/mm/dd/all_exported_blobs. csv*. O aumento do desempenho é esperado com particionamento mais granular. Por exemplo, as consultas em tabelas externas com partições diárias, como a acima, terão um desempenho melhor do que as consultas com tabelas particionadas mensais.

1. A tabela externa está visível no painel esquerdo da interface do usuário da Web

    ![tabela externa na interface do usuário da Web](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Criar uma tabela externa com o formato JSON

Você pode criar uma tabela externa com o formato JSON. Para obter mais informações, consulte [comandos de tabela externa](/azure/kusto/management/externaltables)

1. Use o `.create external table` comando para criar uma tabela chamada *ExternalTableJson*:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. O formato JSON exige uma segunda etapa de criação de mapeamento para colunas, conforme mostrado abaixo. Na consulta a seguir, crie um mapeamento JSON específico chamado *MappingName*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Permissões de tabela externa
 
* O usuário do banco de dados pode criar uma tabela externa. O criador da tabela se torna automaticamente o administrador da tabela.
* O administrador de cluster, banco de dados ou tabela pode editar uma tabela existente.
* Qualquer usuário ou leitor de banco de dados pode consultar uma tabela externa.
 
## <a name="query-an-external-table"></a>Consultar uma tabela externa
 
Para consultar uma tabela externa, use a `external_table()` função e forneça o nome da tabela como o argumento da função. O restante da consulta é a linguagem de consulta Kusto padrão.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Atualmente, não há suporte para o IntelliSense em consultas de tabela externa.

### <a name="query-an-external-table-with-json-format"></a>Consultar uma tabela externa com o formato JSON

Para consultar uma tabela externa com o formato JSON, use `external_table()` a função e forneça o nome da tabela e o nome do mapeamento como os argumentos da função. Na consulta abaixo, se *MappingName* não for especificado, um mapeamento que você criou anteriormente será usado.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Consultar dados externos e ingeridos juntos

Você pode consultar tabelas externas e tabelas de dados ingeridos dentro da mesma consulta. Você [`join`](/azure/kusto/query/joinoperator) [ou`union`](/azure/kusto/query/unionoperator) a tabela externa com dados adicionais do Azure data Explorer, SQL Servers ou outras fontes. Use um [`let( ) statement`](/azure/kusto/query/letstatement) para atribuir um nome abreviado a uma referência de tabela externa.

No exemplo a seguir, *Products* é uma tabela de dados ingerida e *ArchivedProducts* é uma tabela externa que contém dados no Azure data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Consultar tabela externa do *TaxiRides* no cluster de ajuda

O conjunto de dados de exemplo *TaxiRides* contém dados de táxi da cidade de Nova York de [NYC táxi e de limusines Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Criar tabela externa *TaxiRides* 

> [!NOTE]
> Esta seção descreve a consulta usada para criar a tabela externa *TaxiRides* no cluster de *ajuda* . Como essa tabela já foi criada, você pode ignorar esta seção e executar a [consulta *TaxiRides* dados da tabela externa](#query-taxirides-external-table-data). 

1. A consulta a seguir foi usada para criar a tabela externa *TaxiRides* no cluster de ajuda. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. A tabela resultante foi criada no cluster de *ajuda* :

    ![Tabela externa TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Consultar dados da tabela externa do *TaxiRides* 

Entre no para consultar a tabela externa *TaxiRides.* [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Consultar tabela externa do *TaxiRides* sem particionamento

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) na tabela externa *TaxiRides* para representar corridas para cada dia da semana, em todo o conjunto de dados. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Essa consulta mostra o dia mais ocupado da semana. Como os dados não são particionados, essa consulta pode levar muito tempo para retornar resultados (até vários minutos).

![renderizar consulta não particionada](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Consultar tabela externa do TaxiRides com particionamento 

[Execute esta consulta](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) na tabela externa *TaxiRides* mostrando os tipos de táxi cab (amarelo ou verde) usados em janeiro de 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Essa consulta usa particionamento, o que otimiza o tempo de consulta e o desempenho. A consulta filtra em uma coluna particionada (pickup_datetime) e retorna resultados em alguns segundos.

![renderizar consulta particionada](media/data-lake-query-data/taxirides-with-partition.png)
  
Você pode escrever consultas adicionais para executar na tabela externa *TaxiRides* e saber mais sobre os dados. 

## <a name="next-steps"></a>Próximas etapas

Consulte os dados no Azure Data Lake usando o Data Explorer do Azure. Aprenda a [escrever consultas](write-queries.md) e a obter informações adicionais de seus dados.