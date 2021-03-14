---
title: Analisar a transformação de dados no fluxo de dados de mapeamento
description: Analisar documentos de coluna inseridos
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710185"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Analisar transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação analisar para analisar colunas em seus dados que estão no formato de documento. Os tipos atuais com suporte de documentos inseridos que podem ser analisados são texto JSON e delimitado.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Configuração

No painel de configuração analisar transformação, primeiro você escolherá o tipo de dados contido nas colunas que deseja analisar em linha. A transformação Parse também contém as definições de configuração a seguir.

![Configurações de análise](media/data-flow/data-flow-parse-1.png "Analisar")

### <a name="column"></a>Coluna

Semelhante a colunas derivadas e agregações, é aqui que você modificará uma coluna existente selecionando-a no seletor suspenso. Ou você pode digitar o nome de uma nova coluna aqui. O ADF armazenará os dados de origem analisados nesta coluna.

### <a name="expression"></a>Expression

Use o construtor de expressões para definir a origem de sua análise. Isso pode ser tão simples quanto selecionar a coluna de origem com os dados independentes que você deseja analisar, ou você pode criar expressões complexas para análise.

### <a name="output-column-type"></a>Tipo de coluna de saída

Aqui, você configurará o esquema de saída de destino da análise que será gravada em uma única coluna.

![Exemplo de análise](media/data-flow/data-flow-parse-2.png "Exemplo de análise")

Neste exemplo, definimos a análise do campo de entrada "jsonstring", que é texto sem formatação, mas formatado como uma estrutura JSON. Vamos armazenar os resultados analisados como JSON em uma nova coluna chamada "JSON" com este esquema:

```(trade as boolean, customers as string[])```

Consulte a guia inspecionar e a visualização de dados para verificar se a saída está mapeada corretamente.

## <a name="examples"></a>Exemplos

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

### <a name="examples"></a>Exemplos

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Próximas etapas

* Use a [transformação achatamento](data-flow-flatten.md) para linhas dinâmicas em colunas.
* Use a [transformação coluna derivada](data-flow-derived-column.md) para dinamizar as colunas para linhas.
