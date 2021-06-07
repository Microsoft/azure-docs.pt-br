---
title: 'Modelo de Pontuação: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de pontuação em Azure Machine Learning para gerar previsões usando um modelo de classificação ou regressão treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 67d17af4f615907ca50b27ce8fa26973e5869608
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93314257"
---
# <a name="score-model"></a>Pontuar Modelo

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para gerar previsões usando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como usar

1. Adicione o módulo **modelo de Pontuação** ao seu pipeline.

2. Anexe um modelo treinado e um conjunto de dados que contenha novos dado de entrada. 

    Os dados devem estar em um formato compatível com o tipo de modelo treinado que você está usando. O esquema do conjunto de dados de entrada também deve corresponder ao esquema dos dados usados para treinar o modelo.

3. Enviar o pipeline.

## <a name="results"></a>Resultados

Depois de gerar um conjunto de pontuações usando o [modelo de Pontuação](./score-model.md):

+ Para gerar um conjunto de métricas usadas para avaliar a precisão do modelo (desempenho), você pode conectar o conjunto de pontos de Pontuação para [avaliar o modelo](./evaluate-model.md), 
+ Clique com o botão direito do mouse no módulo e selecione **Visualizar** para ver um exemplo dos resultados.
<!-- + To Save the results to a dataset. -->

A pontuação, ou o valor previsto, pode estar em vários formatos diferentes, dependendo do modelo e dos dados de entrada:

- Para modelos de classificação, o [modelo de Pontuação](./score-model.md) gera um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para modelos de regressão, o [modelo de Pontuação](./score-model.md) gera apenas o valor numérico previsto.


## <a name="publish-scores-as-a-web-service"></a>Publicar pontuações como um serviço Web

Um uso comum de pontuação é retornar a saída como parte de um serviço Web de previsão. Para obter mais informações, consulte [este tutorial](../tutorial-designer-automobile-price-deploy.md) sobre como implantar um ponto de extremidade em tempo real com base em um pipeline no designer de Azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.