---
title: 'Tutorial: Usar o pool de SQL sem servidor para analisar os Azure Open Datasets no Azure Synapse Studio'
description: Este tutorial mostra como executar facilmente a análise de dados exploratória combinando diferentes Azure Open Datasets usando o pool de SQL sem servidor e visualizar os resultados no Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d37597f8667c461e8d61f8214483f57eb702c2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007544"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool"></a>Tutorial: Explorar e analisar data lakes com o pool de SQL sem servidor

Neste tutorial, você aprenderá a executar a análise de dados exploratória. Você combinará diferentes Azure Open Datasets usando um pool de SQL sem servidor. Em seguida, você visualizará os resultados no Synapse Studio para o Azure Synapse Analytics.

A função OPENROWSET(BULK...) permite que você acesse arquivos no Armazenamento do Azure. A função [OPENROWSET](develop-openrowset.md) lê o conteúdo de uma fonte de dados remota (por exemplo, o arquivo) e retorna o conteúdo como um conjunto de linhas.

## <a name="automatic-schema-inference"></a>Inferência de esquema automática

Como os dados são armazenados no formato de arquivo parquet, a inferência de esquema automática está disponível. Você pode consultar os dados com facilidade, sem a necessidade de listar os tipos de dados de todas as colunas nos arquivos. Você também pode utilizar o mecanismo de coluna virtual e a função filepath para filtrar um determinado subconjunto de arquivos.

Primeiro, vamos nos familiarizar com os dados de táxis de NYC executando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

O [conjunto de dados de táxi de NYC (Nova York)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) inclui:

- Datas e horas da retirada e entrega.
- Localizações da retirada e entrega. 
- Distâncias das viagens.
- Tarifas discriminadas.
- Tipos de taxa.
- Tipos de pagamento. 
- Contagens de passageiro relatadas pelos motoristas.

Da mesma forma, podemos consultar o conjunto de dados de feriados públicos usando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Por fim, você também pode consultar o conjunto de dados meteorológicos usando a seguinte consulta:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Você pode saber mais sobre o significado das colunas individuais nas descrições dos conjuntos de dados: 
- [Táxi de NYC](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Feriados](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Dados de clima](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Análise de série temporal, sazonalidade e exceções

Você pode resumir com facilidade o número anual de corridas de táxi usando a seguinte consulta:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

O seguinte snippet mostra o resultado do número anual de corridas de táxi:

![Snippet de resultado do número anual de corridas de táxi](./media/tutorial-data-analyst/yearly-taxi-rides.png)

Os dados podem ser visualizados no Synapse Studio, alternando da exibição de **Tabela** para a exibição de **gráfico**. Você pode escolher entre diferentes tipos de gráfico, tais como: **área**, **barras**, **colunas**, **linha**, **pizza** e **dispersão**. Neste caso, plote o gráfico de **colunas** com a coluna **Categoria** definida com o valor **current_year**:

![Gráfico de colunas mostrando o número de corridas por ano](./media/tutorial-data-analyst/column-chart-rides-year.png)

Nessa visualização, você pode ver uma tendência de diminuição dos números de corridas ao longo dos anos. Supostamente, essa queda se deve ao recente aumento da popularidade das empresas de compartilhamento de caronas.

> [!NOTE]
> No momento da elaboração deste tutorial, os dados de 2019 ainda estavam incompletos. Como resultado, há uma enorme queda no número de corridas desse ano.

Em seguida, vamos concentrar nossa análise em um ano específico: por exemplo, 2016. A seguinte consulta retorna o número diário de corridas durante aquele ano:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

O seguinte snippet mostra o resultado dessa consulta:

![Snippet de resultado do número diário de corridas em 2016](./media/tutorial-data-analyst/daily-rides.png)

Novamente, você pode visualizar com facilidade os dados plotando o gráfico de **colunas** com a coluna **Categoria** definida com o valor **current_day** e a coluna **Legend (series)** definida com o valor **rides_per_day**.

![Gráfico de colunas mostrando o número diário de corridas em 2016](./media/tutorial-data-analyst/column-chart-daily-rides.png)

No gráfico de plotagem, você pode ver que há um padrão semanal, com pico diário aos sábados. Durante os meses de verão, há menos corridas de táxi devido ao período de férias. Há também algumas quedas significativas no número de corridas de táxi sem um padrão claro de quando e por que elas ocorrem.

Em seguida, vamos ver se a queda nas corridas se correlaciona com feriados públicos. Podemos ver se há uma correlação unindo o conjunto de dados de corridas de Táxi de NYC com o conjunto de dados de Feriados Públicos:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![Visualização de resultado dos conjunto de dados de corridas de táxis e de feriados públicos em NYC](./media/tutorial-data-analyst/rides-public-holidays.png)

Desta vez, queremos realçar o número de corridas de táxi durante os feriados públicos. Para tanto, escolheremos **nenhum** para a coluna **Categoria** e **rides_per_day** e **holiday** como as colunas **Legend (series)** .

![Gráfico de plotagem do número de corridas de táxi durante os feriados públicos em NYC](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

No gráfico de plotagem, é possível ver que, durante os feriados públicos, o número de corridas de táxi é menor. No entanto, ainda há uma grande queda não explicada no dia 23 de janeiro. Vamos examinar o clima em NYC naquele dia consultando o conjunto de dados meteorológicos:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Visualização de resultado do conjunto de dados meteorológicos de NYC](./media/tutorial-data-analyst/weather-data-set-visualization.png)

Os resultados da consulta indicam que a queda no número de corridas de táxis ocorreu porque:

- houve uma nevasca de alta intensidade (aprox. 30 cm de espessura) naquele dia em NYC.
- Fez muito frio (a temperatura ficou abaixo de zero graus Celsius).
- Vento muito (aproximadamente 10 m/s).

Este tutorial mostrou como o analista de dados pode executar rapidamente análises de dados exploratórias, combinar com facilidade diferentes conjuntos de dados usando o pool de SQL sem servidor e visualizar os resultados usando o Azure Synapse Studio.

## <a name="next-steps"></a>Próximas etapas

Para aprender como conectar o pool de SQL sem servidor ao Power BI Desktop e criar relatórios, confira o artigo [Conectar o pool de SQL sem servidor ao Power BI Desktop e criar relatórios](tutorial-connect-power-bi-desktop.md).

Para saber como usar tabelas externas no pool de SQL sem servidor, confira [Usar tabelas externas com o SQL do Synapse](develop-tables-external-tables.md?tabs=sql-pool)
 
