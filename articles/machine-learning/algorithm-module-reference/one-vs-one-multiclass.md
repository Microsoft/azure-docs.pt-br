---
title: Multiclasse um-vs-um
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo multiclasse One-vs-One em Azure Machine Learning para criar um modelo de classificação multiclasse a partir de um Ensemble de modelos de classificação binária.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592899"
---
# <a name="one-vs-one-multiclass"></a>Multiclasse um-vs-um

Este artigo descreve como usar o módulo multiclasse One-vs-One no Azure Machine Learning designer. O objetivo é criar um modelo de classificação que possa prever várias classes, usando a abordagem *um-versus-um* .

Esse módulo é útil para criar modelos que preveem três ou mais resultados possíveis, quando o resultado depende de variáveis de previsão contínuas ou categóricas. Esse método também permite usar métodos de classificação binária para problemas que exigem várias classes de saída.

### <a name="more-about-one-versus-one-models"></a>Mais sobre modelos um-versus um

Alguns algoritmos de classificação permitem o uso de mais de duas classes por design. Outros restringem os resultados possíveis para um de dois valores (um modelo binário ou de duas classes). Mas mesmo algoritmos de classificação binária podem ser adaptados para tarefas de classificação de várias classes por meio de uma variedade de estratégias. 

Esse módulo implementa o método um-versus-um, no qual um modelo binário é criado por par de classe. No momento da previsão, a classe que recebeu a maioria dos votos está selecionada. Como é necessário ajustar os `n_classes * (n_classes - 1) / 2` classificadores, esse método é geralmente mais lento do que um, devido à sua complexidade o (n_classes ^ 2). No entanto, esse método pode ser vantajoso para algoritmos como algoritmos de kernel que não são bem dimensionados com o `n_samples` . Isso ocorre porque cada problema de aprendizado individual envolve apenas um pequeno subconjunto dos dados, ao passo que, com um-versus-todos, o DataSet completo é usado `n_classes` vezes.

Em essência, o módulo cria um Ensemble de modelos individuais e, em seguida, mescla os resultados, para criar um modelo único que prevê todas as classes. Qualquer classificador binário pode ser usado como base para um modelo um-versus um.  

Por exemplo, digamos que você configure um modelo de [máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) e forneça isso como entrada para o módulo de multiclasse um-vs-um. O módulo criará modelos de máquina de vetor de suporte de duas classes para todos os membros da classe de saída. Em seguida, ele aplicaria o método um-versus-um para combinar os resultados de todas as classes.  

O módulo usa OneVsOneClassifier de sklearn e você pode aprender mais detalhes [aqui](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html).

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Como configurar o classificador multiclasse One-vs-um  

Esse módulo cria um Ensemble de modelos de classificação binária para analisar várias classes. Para usar este módulo, você precisa configurar e treinar primeiro um modelo de *classificação binária* . 

Você conecta o modelo binário ao módulo de multiclasse One-vs-One. Em seguida, você treina o Ensemble de modelos usando o [modelo](train-model.md) de treinamento com um conjunto de acordo com o título.

Quando você combina os modelos, a multiclasse um-vs-um cria vários modelos de classificação binária, otimiza o algoritmo para cada classe e, em seguida, mescla os modelos. O módulo executa essas tarefas, embora o conjunto de módulos de treinamento possa ter vários valores de classe.

1. Adicione o módulo de multiclasse One-vs-One ao seu pipeline no designer. Você pode encontrar esse módulo em **Machine Learning-Initialize**, na categoria **classificação** .

   O classificador multiclasse One-vs-One não tem nenhum parâmetro configurável. Todas as personalizações devem ser feitas no modelo de classificação binária que é fornecido como entrada.

2. Adicione um modelo de classificação binária ao pipeline e configure esse modelo. Por exemplo, você pode usar uma [máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) ou [árvore de decisão aumentada de duas classes](two-class-boosted-decision-tree.md).

3. Adicione o módulo [modelo de treinamento](train-model.md) ao seu pipeline. Conecte o classificador não treinado que é a saída de multiclasse um-vs-um.

4. Na outra entrada do [modelo de treinamento](train-model.md), conecte um conjunto de dados de treinamento rotulado que tenha vários valores de classe.

5. Enviar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento, você pode usar o modelo para fazer previsões multiclasse.

Como alternativa, você pode passar o classificador não treinado para o modelo de validação [cruzada para validações](cross-validate-model.md) cruzadas em relação a um conjunto de uma validação rotulada.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
