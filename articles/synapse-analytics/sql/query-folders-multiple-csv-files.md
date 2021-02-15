---
title: Pastas de consulta e vários arquivos usando o pool SQL sem servidor
description: O pool SQL sem servidor dá suporte à leitura de vários arquivos/pastas usando curingas, que são semelhantes aos curingas usados no sistema operacional Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462624"
---
# <a name="query-folders-and-multiple-files"></a>Consultar pastas e vários arquivos  

Neste artigo, você aprenderá a escrever uma consulta usando o pool SQL sem servidor no Azure Synapse Analytics.

O pool SQL sem servidor dá suporte à leitura de vários arquivos/pastas usando curingas, que são semelhantes aos curingas usados no sistema operacional Windows. No entanto, há maior flexibilidade, já que vários curingas são permitidos.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é **criar um banco de dados** no qual você executará as consultas. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados nessas amostras.

Você usará a pasta *CSV/táxi* para seguir as consultas de exemplo. Ele contém dados de NYC táxi-amarelo de táxi, de julho de 2016 a junho de 2018. Os arquivos em *CSV/táxi* são nomeados após o ano e o mês usando o seguinte padrão: yellow_tripdata_ <year> - <month> . csv

## <a name="read-all-files-in-folder"></a>Ler todos os arquivos na pasta

O exemplo a seguir lê todos os arquivos de dados de táxi amarelo NYC da pasta *CSV/táxi* e retorna o número total de passageiros e corridas por ano. Ele também mostra o uso de funções de agregação.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-subset-of-files-in-folder"></a>Ler subconjunto de arquivos na pasta

O exemplo a seguir lê os arquivos de dados de táxi de 2017 NYC da pasta *CSV/táxi* usando um curinga e retorna o valor de tarifa total por tipo de pagamento.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

## <a name="read-folders"></a>Ler pastas

O caminho que você fornece para OPENROWSET também pode ser um caminho para uma pasta. As seções a seguir incluem esses tipos de consulta.

### <a name="read-all-files-from-specific-folder"></a>Ler todos os arquivos de uma pasta específica

Você pode ler todos os arquivos em uma pasta usando o curinga de nível de arquivo, conforme mostrado em [ler todos os arquivos na pasta](#read-all-files-in-folder). Mas há uma maneira de consultar uma pasta e consumir todos os arquivos dentro dessa pasta.

Se o caminho fornecido no OPENROWSET apontar para uma pasta, todos os arquivos nessa pasta serão usados como uma fonte para a consulta. A consulta a seguir lerá todos os arquivos na pasta *CSV/táxi* .

> [!NOTE]
> Observe a existência do/no final do caminho na consulta abaixo. Ele denota uma pasta. Se o/for omitido, a consulta irá direcionar um arquivo chamado *táxi* em vez disso.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-all-files-from-multiple-folders"></a>Ler todos os arquivos de várias pastas

É possível ler arquivos de várias pastas usando um caractere curinga. A consulta a seguir lerá todos os arquivos de todas as pastas localizadas na pasta *CSV* que têm nomes começando com *t* e terminando com *i*.

> [!NOTE]
> Observe a existência do/no final do caminho na consulta abaixo. Ele denota uma pasta. Se o/for omitido, a consulta irá direcionar os arquivos chamados *t &ast; i* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Como você tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler todos os arquivos na pasta](#read-all-files-in-folder).

## <a name="traverse-folders-recursively"></a>Percorrer pastas recursivamente

O pool SQL sem servidor pode percorrer as pastas recursivamente se você especificar/* * no final do caminho. A consulta a seguir lerá todos os arquivos de todas as pastas e subpastas localizadas na pasta *CSV* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

## <a name="multiple-wildcards"></a>Vários curingas

Você pode usar vários curingas em diferentes níveis de caminho. Por exemplo, você pode enriquecer a consulta anterior para ler arquivos somente com 2017 dados, de todas as pastas que os nomes começam com *t* e terminar com *i*.

> [!NOTE]
> Observe a existência do/no final do caminho na consulta abaixo. Ele denota uma pasta. Se o/for omitido, a consulta irá direcionar os arquivos chamados *t &ast; i* .
> Há um limite máximo de 10 curingas por consulta.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Como você tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler o subconjunto de arquivos na pasta](#read-subset-of-files-in-folder) e [ler todos os arquivos de uma pasta específica](#read-all-files-from-specific-folder). Cenários de uso de curinga mais complexos são abordados em [arquivos de consulta parquet](query-parquet-files.md).

## <a name="next-steps"></a>Próximas etapas

Mais informações podem ser encontradas no artigo [consultar arquivos específicos](query-specific-files.md) .
