---
title: Obter todas as APIs de conjuntos de os
description: Use esta API para obter todos os conjuntos de valores disponíveis para análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583666"
---
# <a name="get-all-datasets-api"></a>Obter todas as APIs de conjuntos de os

A API obter todos os conjuntos de itens Obtém todos os conjuntos de valores disponíveis. Conjuntos de valores listam as tabelas, colunas, métricas e intervalos de tempo.

**Sintaxe da solicitação**

| **Método** | **URI da solicitação** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Cabeçalho da solicitação**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatórios. O token de acesso do Azure Active Directory (AD do Azure) no formulário `Bearer <token>` |
| Tipo de conteúdo | string | `Application/JSON` |

**Parâmetro de caminho**

Nenhum

**Parâmetro de consulta**

| **Nome do parâmetro** | **Tipo** | **Necessário** | **Descrição** |
| --- | --- | --- | --- |
| `datasetName` | Cadeia de caracteres | No | Filtrar para obter detalhes de apenas um conjunto de informações |

**Carga de solicitação**

Nenhum

**Glossário**

Não

**Resposta**

A carga de resposta é estruturada da seguinte maneira:

Código de resposta: 200, 400, 401, 403, 404, 500

Exemplo de carga de resposta:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Glossário**

Esta tabela define os principais elementos na resposta:

| **Parâmetro** | **Descrição** |
| --- | --- |
| `DatasetName` | Nome do conjunto de objetos que este objeto de matriz define |
| `SelectableColumns` | Colunas brutas que podem ser especificadas nas colunas selecionadas |
| `AvailableMetrics` | Nomes de coluna de agregação/métrica que podem ser especificados nas colunas de seleção |
| `AvailableDateRanges` | Intervalo de datas que pode ser usado em consultas de relatório para o conjunto de dados |
| `NextLink` | Link para a próxima página se os dados forem paginados |
| `TotalCount` | Número de conjuntos de itens na matriz de valores |
| `StatusCode` | Código de resultado. Os valores possíveis são 200, 400, 401, 403, 500 |
