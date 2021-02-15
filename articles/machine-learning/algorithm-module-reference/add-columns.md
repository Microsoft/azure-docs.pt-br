---
title: 'Adicionar colunas: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo adicionar colunas no designer de Azure Machine Learning do tipo "arrastar e soltar" para concatenar dois conjuntos de valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 36de827dff239dbeebc66e330a76b7a65fefb909
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421950"
---
# <a name="add-columns-module"></a>Adicionar módulo de colunas

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para concatenar dois conjuntos de valores. Você combina todas as colunas dos dois conjuntos de valores que você especifica como entradas para criar um único conjunto de informações. Se você precisar concatenar mais de dois conjuntos de os, use várias instâncias de **adicionar colunas**.



## <a name="how-to-configure-add-columns"></a>Como configurar adicionar colunas
1. Adicione o módulo **adicionar colunas** ao seu pipeline.

2. Conecte os dois conjuntos de os que você deseja concatenar. Se você quiser combinar mais de dois conjuntos de os, poderá encadear várias combinações de **adicionar colunas**.

    - É possível combinar duas colunas que têm um número diferente de linhas. O conjunto de resultados de saída é preenchido com valores ausentes para cada linha na coluna de origem menor.

    - Você não pode escolher colunas individuais para adicionar. Todas as colunas de cada conjunto de linhas são concatenadas quando você usa **adicionar colunas**. Portanto, se você quiser adicionar apenas um subconjunto das colunas, use selecionar colunas no conjunto de conjuntos para criar um conjunto de um DataSet com as colunas desejadas.

3. Enviar o pipeline.

### <a name="results"></a>Resultados
Após a execução do pipeline:

- Para ver as primeiras linhas do novo conjunto de registros, clique com o botão direito do mouse no módulo **adicionar colunas** e selecione Visualizar. Ou selecione o módulo e alterne para a guia **saídas** no painel direito, clique no ícone de histograma nas saídas de **porta** para visualizar o resultado.

O número de colunas no novo conjunto de dados é igual à soma das colunas de ambos os conjuntos de dados de entrada.

Se houver duas colunas com o mesmo nome nos conjuntos de dados de entrada, um sufixo numérico será adicionado ao nome da coluna. Por exemplo, se houver duas instâncias de uma coluna denominadas TargetOutcome, a coluna da esquerda será renomeada TargetOutcome_1 e a coluna direita será renomeada TargetOutcome_2.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 