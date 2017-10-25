---
title: Python de exemplo para derivar novas colunas no preparo dos dados do Azure Machine Learning | Microsoft Docs
description: "Este documento fornece exemplos de código do Python para criar novas colunas na preparação de dados do Azure ML"
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
ms.date: 09/12/2017
ms.openlocfilehash: 9a6e331e622b007232a62b34c2220d60d1d050ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-column-transforms-python"></a>Exemplo de transformações de coluna personalizadas (Python) 
O nome dessa transformação no menu é 'Adicionar Coluna (Script)'

Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md)

## <a name="test-equivalence-and-replace-values"></a>Testar equivalência e substituir valores 
Se o valor na Col1 for menor que 4, a nova coluna deverá ter um valor de 1, do contrário, terá o valor de 2 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Data e hora atuais 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Typecasting 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Avaliar quanto à nulidade 
Se a Col1 contiver um valor nulo, marque a nova coluna como Ruim, caso contrário, marque-a como Boa 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nova coluna computada 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Computação de época 
Número de segundos desde a Época Unix (assumindo que Col1 já é uma data) 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```





