---
title: Especificação de consulta personalizada
description: Saiba como usar consultas personalizadas para extrair dados de forma programática de tabelas de análise para suas ofertas no Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583681"
---
# <a name="custom-query-specification"></a>Especificação de consulta personalizada

Os parceiros podem usar essa especificação de consulta para formular facilmente consultas personalizadas para extrair dados de tabelas de análise. As consultas podem ser usadas para selecionar apenas as colunas e métricas desejadas que correspondam a um determinado critério. No coração da especificação de linguagem está a definição de conjunto de pesquisa na qual uma consulta personalizada pode ser gravada.

## <a name="datasets"></a>Conjunto de dados

Da mesma forma que algumas consultas são executadas em um banco de dados que tem tabelas e colunas, uma consulta personalizada funciona em conjuntos de dados que têm colunas e métricas. A lista completa de conjuntos de valores disponíveis para formular uma consulta pode ser obtida usando a API DataSets.

Aqui está um exemplo de um conjunto de um DataSet mostrado como um JSON.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Partes de um conjunto de uma

- Um nome de DataSet é como um nome de tabela de banco de dados. Por exemplo, ISVUsage. Um conjunto de linhas tem uma lista de colunas que podem ser selecionadas, como MarketplaceSubscriptionId.
- Um conjunto de dados também tem métricas, que são como funções de agregação em um Database. Por exemplo, NormalizedUsage.
- Há intervalos de tempo fixos sobre os quais os dados podem ser exportados.

### <a name="formulating-a-query-on-a-dataset"></a>Formular uma consulta em um DataSet

Essas são algumas consultas de exemplo que mostram como extrair vários tipos de dados.

| Consulta | Descrição |
| ------------ | ------------- |
| **Selecione** MarketplaceSubscriptionId, CustomerId **de** ISVUsage **TIMESPAN LAST_MONTH** | Essa consulta obterá todos `MarketplaceSubscriptionId` os exclusivos e seus correspondentes `CustomerId` no último mês. |
| **Selecione** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **da** ISVUSAGE **ordem por** EstimatedExtendedChargeCC **limite** 10 | Essa consulta receberá as 10 principais assinaturas em ordem decrescente do número de licenças vendidas em cada assinatura. |
| **Selecione** CustomerId, NormalizedUsage, RawUsage **de** ISVUsage **order by NormalizedUsage,** **em que** NormalizedUsage > 100000 **order by** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Essa consulta obterá a NormalizedUsage e a RawUsage de todos os clientes que têm NormalizedUsage maior que 100.000. |
| **Selecione** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **de** ISVUsage em **que** CustomerID (' 2a31c234-1f4e-4c60-909e-76d234f93161 ', ' 80780748-3f9a-11EB-b378-0242ac130002 ') | Essa consulta receberá o `MarketplaceSubscriptionId` e a receita gerada para cada mês pelos dois `CustomerId` valores: `2a31c234-1f4e-4c60-909e-76d234f93161` e `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Especificação de consulta

Esta seção descreve a definição e a estrutura de consulta.

### <a name="grammar-reference"></a>Referência de gramática

Esta tabela descreve os símbolos usados em consultas.

| Símbolo | Significado |
| ------------ | ------------- |
| ? | Opcional |
| * | Zero ou mais |
| + | Um ou mais |
| &#124; | Ou/um da lista |
| | |

### <a name="query-definition"></a>Definição da consulta

A instrução de consulta tem as seguintes cláusulas: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? e TimeSpan?.

- **SelectClause**: **selecione** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: colunas e métricas definidas no conjunto de um
- **FromClause**: **do** DataSetName
    - **DataSetName**: nome do conjunto de acordo definido no conjunto de
- **WhereClause**: **Where** FilterCondition (**e** FilterCondition) *
    - **FilterCondition**: valor do operador ColumOrMetricName
        - **Operador**: = | > | < | >= | <= |! = | COMO | NÃO COMO | EM | NÃO ESTÁ EM
        - **Valor**: número | StringLiteral | Multinumeralist | Multistringlist 
            - **Número**:-? [0-9] + (. [0-9] [0-9] *)?
            - **StringLiteral**: ' [a-Za-z0-9_] * '
            - **Multinumeralist**: (número (, número) *)
            - **Multistringlist**: (StringLiteral (, StringLiteral) *)
- **OrderClause**: **order by** OrderCondition (, OrderCondition) *
    - **OrderCondition**: ColumOrMetricName (**ASC**  |  **desc**) *
    - **LimitClause**: **limite** [0-9] +
    - **TimeSpan**: **TimeSpan** (hoje | ONTEM | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | EXISTÊNCIA

### <a name="query-structure"></a>Estrutura de consulta

Uma consulta de relatório é composta por várias partes:

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- PERÍODO

Cada parte é descrita abaixo.

#### <a name="select"></a>SELECT

Essa parte da consulta especifica as colunas que serão exportadas. As colunas que podem ser selecionadas são os campos listados em `selectableColumns` e as `availableMetrics` seções de um conjunto de uma. As linhas exportadas finais sempre conterão valores distintos nas colunas selecionadas. Por exemplo, não haverá nenhuma linha duplicada no arquivo exportado. As métricas serão calculadas para cada combinação distinta das colunas selecionadas.

**Exemplo**:
- **Selecione** `OfferName` , `NormalizedUsage`

#### <a name="from"></a>FROM

Essa parte da consulta indica o conjunto de dados a partir do qual é necessário exportar o dado. O nome do conjunto de dados fornecido aqui precisa ser um nome de conjunto de dados válido retornado pela API de conjuntos de dados.

**Exemplo**:
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

Essa parte da consulta é usada para especificar condições de filtro no conjunto de os. Somente as linhas correspondentes a todas as condições listadas nesta cláusula estarão presentes no arquivo exportado final. A condição de filtro pode estar em qualquer uma das colunas listadas em `selectableColumns` e `availableMetrics` . Os valores especificados na condição de filtro podem ser uma lista de números ou uma lista de cadeias de caracteres somente quando o operador é `IN` ou `NOT IN` . Os valores sempre podem ser fornecidos como uma cadeia de caracteres literal e serão convertidos para os tipos nativos de colunas. Várias condições de filtro precisam ser separadas com uma `AND` operação.

**Exemplo**:

- MarketplaceSubscriptionId = ' 868368da-957d-4959-8992-3c12dc7e6260 '
- CustomerName **como** '% emissor% '
- CustomerId **não está em** (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity = ' 100 '
    - MarketplaceSubscriptionId = ' 7b487ac0-ce12-b732-dcd6-91a1e4e74a50 ' e CustomerId = ' 0f8b7fa0-eb83-a183-1225-ca153ef807aa '

#### <a name="order-by"></a>ORDER BY

Esta parte da consulta especifica os critérios de ordenação das linhas exportadas. As colunas nas quais a ordenação pode ser definida precisam ser do `selectableColumns` e `availableMetrics` do conjunto de um. Se não houver nenhuma direção de ordenação especificada, ela será padronizada `DESC` na coluna. A ordenação pode ser definida em várias colunas, separando os critérios com uma vírgula.

**Exemplo**:

- **ordenar por** NormalizedUsage **ASC**, ESTIMATEDEXTENDEDCHARGE (CC) **desc**
- **ordenar por** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

Essa parte da consulta especifica o número de linhas que serão exportadas. O número especificado precisa ser um inteiro positivo diferente de zero.

#### <a name="timespan"></a>PERÍODO

Essa parte da consulta especifica a duração de tempo para a qual os dados precisam ser exportados. Os valores possíveis devem ser do `availableDateRanges` campo na definição do conjunto de valores.

### <a name="case-sensitivity-in-query-specification"></a>Distinção de maiúsculas e minúsculas na especificação de consulta

A especificação é completamente não diferencia maiúsculas de minúsculas. Palavras-chave predefinidas, nomes de coluna e valores podem ser especificados usando letras maiúsculas ou minúsculas.

## <a name="see-also"></a>Veja também

- [Lista de consultas do sistema](analytics-system-queries.md)
