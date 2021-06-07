---
title: Folha de referências de algoritmos do Machine Learning – designer
titleSuffix: Azure Machine Learning
description: Uma folha de referências de algoritmos do Machine Learning que pode ser impressa ajuda a escolher o algoritmo certo para o modelo de previsão no designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100367049"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Folha de referências de algoritmos do Machine Learning para o designer do Azure Machine Learning

A **Folha de referências de algoritmos do Azure Machine Learning** ajuda a escolher o algoritmo certo do designer para o modelo de análise preditiva.

O Azure Machine Learning tem uma grande biblioteca de algoritmos das famílias ***classificação** _, _*_sistemas de recomendação_*_, _*_clustering_*_, _*_detecção de anomalias_*_, _*_regressão_*_ e _ *_análise de texto_* *. Cada um foi projetado para atender a um tipo diferente de problema de aprendizado de máquina.

Veja outras diretrizes em [Como selecionar algoritmos](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Baixe: Folha de referências de algoritmos do Machine Learning

**Baixe a folha de consulta daqui: [Folha de consulta de algoritmos do Machine Learning (11 x 17 pol.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Folha de referências de algoritmos do Machine Learning: saiba como escolher um algoritmo do Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Baixar e imprimir a folha de consulta do algoritmo do Machine Learning em tamanho tabloide para mantê-lo à mão e obter ajuda para escolher um algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Como usar a Folha de referências de algoritmos do Machine Learning

As sugestões oferecidas nessa página de dicas úteis de algoritmo são aproximadas às regras de bolso. Algumas podem ser ajustadas e algumas podem ser flagrantemente violadas. A folha de referências serve como um ponto de partida. Não tenha medo de comparar vários algoritmos em seus dados. Simplesmente não existe substituto para entender os princípios de cada algoritmo e o sistema que gerou os dados.

Cada algoritmo de aprendizado de máquina tem seu próprio estilo ou tendência indutiva. Para um problema específico, vários algoritmos podem ser apropriados, e um algoritmo pode ser mais adequado do que outros. Mas nem sempre é possível saber com antecedência qual é a melhor opção. Em casos como esse, vários algoritmos estão listados juntos na folha de consulta. Uma estratégia apropriada seria um algoritmo e se os resultados ainda não estiverem satisfatórios, tentar os outros. 

Para saber mais sobre os algoritmos no designer do Azure Machine Learning, acesse a [referência de algoritmo e módulo](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Tipos de aprendizado de máquina

Há três categorias principais de aprendizado de máquina: *aprendizado supervisionado*, *aprendizado sem supervisão* e *aprendizado de reforço*.

### <a name="supervised-learning"></a>Aprendizado supervisionado

Em aprendizado supervisionado, cada ponto de dados é rotulado ou associado a uma categoria ou um valor de interesse. Um exemplo de um rótulo categórico é atribuir uma imagem como, por exemplo, um gato ou um cão. Um exemplo de um rótulo de valor é o preço de venda associado a um carro usado. O objetivo do aprendizado supervisionado é estudar vários exemplos rotulados como esses e, em seguida, conseguir fazer previsões sobre os pontos de dados futuros. Por exemplo, identificar novas fotos com o animal correto ou atribuir preços de vendas precisos a outros carros usados. Este é um tipo popular e útil de aprendizado de máquina.

### <a name="unsupervised-learning"></a>Aprendizado não supervisionado

No aprendizado não supervisionado, os pontos de dados não têm rótulos associados a eles. Em vez disso, a meta de um algoritmo de aprendizado sem supervisão é organizar os dados de alguma forma ou descrever sua estrutura. O aprendizado não supervisionado divide os dados em grupos, como faz o K-means, ou encontra diferentes maneiras de consultar dados complexos para que eles pareçam mais simples.

### <a name="reinforcement-learning"></a>Aprendizado de reforço

No aprendizado de reforço, o algoritmo escolhe uma ação em resposta a cada ponto de dados. É uma abordagem comum em robótica, em que o conjunto de leituras do sensor, em um ponto no tempo, é um ponto de dados e o algoritmo deve escolher a próxima ação do robô. Também é um ajuste natural para aplicativos da Internet das Coisas. O algoritmo de aprendizado também recebe um sinal de recompensa pouco tempo depois, indicando se a decisão foi boa. Com base no sinal, o algoritmo modifica sua estratégia para alcançar a recompensa mais alta. 

## <a name="next-steps"></a>Próximas etapas

* Veja outras diretrizes para a [seleção de algoritmos](how-to-select-algorithms.md)

* [Saiba mais sobre o estúdio do Azure Machine Learning e o portal do Azure](overview-what-is-azure-ml.md).

* [Tutorial: criar um modelo de previsão no designer do Azure Machine Learning](tutorial-designer-automobile-price-train-score.md).

* [Conheça as diferenças entre o aprendizado profundo e o aprendizado de máquina](concept-deep-learning-vs-machine-learning.md).
