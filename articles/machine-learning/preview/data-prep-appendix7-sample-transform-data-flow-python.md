---
title: "Transformações de fluxo de dados de transformação de exemplo possíveis com a Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de transformações de fluxo de dados de transformações possíveis com a preparação de dados do Azure ML"
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
ms.openlocfilehash: f43f65ca89349fc790684e9bd7acd2f19e15abe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Exemplo de transformações de fluxo de dados personalizadas (Python) 
O nome dessa transformação no menu é “Transformar fluxo de dados (Script)” Antes de ler esse apêndice, leia a [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md)

## <a name="transform-frame"></a>Transformar quadro
### <a name="create-a-new-column-dynamically"></a>Criar uma nova coluna dinamicamente 
Cria uma coluna dinamicamente (city2) e reconcilia várias versões diferentes de São Francisco para uma da coluna de cidade existente.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Adicionar nova agregações
Cria um novo quadro com as primeiras e as últimas agregações calculadas para a coluna de pontuação agrupadas pela coluna risk_category
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Realizar winsoring de uma coluna 
Reformula os dados para atender a uma fórmula para reduzir as exceções em uma coluna
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Transformar fluxo de dados
### <a name="fill-down"></a>Preencher 
O preenchimento requer duas transformações.
Supondo que os dados são semelhantes ao seguinte:


|Estado         |City       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Califórnia    |Los Angeles|
|              |San Diego  |
|              |San José   |
|Texas         |Dallas     |
|              |San Antonio|
|              |Houston    |

Em primeiro lugar, crie uma Transformação 'Adicionar Coluna (Script)' que contém o código a seguir
```python
    row['State'] if len(row['State']) > 0 else None
```
Agora crie uma Transformação Transformar Fluxo de Dados (Script) que contém o código a seguir
```python
    df = df.fillna( method='pad')
```

E agora os dados são semelhantes ao seguinte:

|Estado         |newState         |City       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|Califórnia    |Califórnia    |Los Angeles|
|              |Califórnia    |San Diego  |
|              |Califórnia    |San José   |
|Texas         |Texas         |Dallas     |
|              |Texas         |San Antonio|
|              |Texas         |Houston    |

