---
title: "Expressões de filtro de exemplo possíveis com a Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de expressões de filtro possíveis com a preparação de dados do Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: a80e43f84b518a7c9ce609fbebe34c531e1ab187
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-filter-expressions-python"></a>Exemplo de expressões de filtro (Python) 
Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md)

## <a name="filter-with-equivalence-test"></a>Filtrar com teste de equivalência
Filtrar somente as linhas em que o valor de Col2 (numérico) é maior que 4 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrar com várias colunas 
Filtrar somente as linhas em que Col1 contém o valor “Bom” e Col2 contém o valor (numérico) 1 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtro de teste com null
Filtrar somente as linhas em que Col1 tem um valor nulo 
```python
    pd.isnull(row["Col1"])
```
