---
title: "Saídas/destinos de exemplo possíveis com a Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de saídas/destinos de dados personalizados com a preparação de dados do Azure ML"
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
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-destination-connections-python"></a>Exemplo de conexões de destino (Python) 
Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md)


### <a name="write-to-excel"></a>Gravar no Excel 


A gravação no Excel requer uma biblioteca adicional, a adição de novas bibliotecas está documentada na visão geral de extensibilidade. `openpyxl` é a biblioteca que precisa ser adicionada.

Antes de gravar, algumas outras alterações podem ser necessárias. Alguns dos tipos de dados usados na preparação de dados não têm suporte em alguns formatos de destino. Como exemplo, se existirem objetos “Error”, eles não serão serializados corretamente no Excel. Portanto, uma transformação "Substituir Valores de Erro", que remove os erros de todas as colunas, é necessária antes de tentar gravar no Excel.

Supondo que todo o trabalho acima foi concluído, as linhas a seguir gravam a tabela de dados em uma única planilha em um documento Excel. Adicione uma transformação Gravar Fluxo de Dados (Script) e insira o código a seguir em uma seção de expressão:


#### <a name="on-windows"></a>No Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>No macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
