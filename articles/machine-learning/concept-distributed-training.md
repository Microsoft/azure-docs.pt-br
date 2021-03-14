---
title: O que é treinamento distribuído?
titleSuffix: Azure Machine Learning
description: Saiba que tipo de treinamento distribuído Azure Machine Learning dá suporte e integrações de estrutura de software livre disponíveis para treinamento distribuído.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f87175500fcf5bdbcf9a5c2f499f6bab96b37b63
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102498958"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Treinamento distribuído com o Azure Machine Learning

Neste artigo, você aprenderá sobre o treinamento distribuído e como Azure Machine Learning dá suporte a ele para modelos de aprendizado profundo. 

No treinamento distribuído, a carga de trabalho para treinar um modelo é dividida e compartilhada entre vários mini processadores, chamados de nós de trabalho. Esses nós de trabalho funcionam em paralelo para acelerar o treinamento de modelo. O treinamento distribuído pode ser usado para modelos de ML tradicionais, mas é mais adequado para tarefas intensivas de computação e tempo, como [aprendizado profundo](concept-deep-learning-vs-machine-learning.md) para o treinamento de redes neurais profundas. 

## <a name="deep-learning-and-distributed-training"></a>Aprendizado avançado e treinamento distribuído 

Há dois tipos principais de treinamento distribuído: [paralelismo de dados](#data-parallelism) e [paralelismo de modelo](#model-parallelism). Para treinamento distribuído sobre modelos de aprendizado profundo, o [SDK do Azure Machine Learning no Python](/python/api/overview/azure/ml/intro) dá suporte a integrações com estruturas populares, PyTorch e TensorFlow. Ambas as estruturas empregam o paralelismo de dados para treinamento distribuído e podem aproveitar o [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) para otimizar as velocidades de computação. 

* [Treinamento distribuído com PyTorch](how-to-train-pytorch.md#distributed-training)

* [Treinamento distribuído com o TensorFlow](how-to-train-tensorflow.md#distributed-training)

Para modelos de ML que não exigem treinamento distribuído, consulte [treinar modelos com Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) para diferentes maneiras de treinar modelos usando o SDK do Python.

## <a name="data-parallelism"></a>Paralelismo de dados

O paralelismo de dados é o mais fácil de implementar as duas abordagens de treinamento distribuídas e é suficiente para a maioria dos casos de uso.

Nessa abordagem, os dados são divididos em partições, em que o número de partições é igual ao número total de nós disponíveis, no cluster de computação. O modelo é copiado em cada um desses nós de trabalho, e cada operador opera em seu próprio subconjunto dos dados. Tenha em mente que cada nó precisa ter a capacidade de dar suporte ao modelo que está sendo treinado, ou seja, o modelo precisa se ajustar totalmente a cada nó. O diagrama a seguir fornece uma demonstração visual dessa abordagem.

![Paralelismo de dados-conceito-diagrama](./media/concept-distributed-training/distributed-training.svg)

Cada nó computa de forma independente os erros entre suas previsões para seus exemplos de treinamento e as saídas rotuladas. Por sua vez, cada nó atualiza seu modelo com base nos erros e deve comunicar todas as suas alterações aos outros nós para atualizar seus modelos correspondentes. Isso significa que os nós de trabalho precisam sincronizar os parâmetros do modelo, ou gradientes, no final da computação do lote para garantir que eles estejam treinando um modelo consistente. 

## <a name="model-parallelism"></a>Paralelismo de modelo

No paralelismo de modelo, também conhecido como paralelismo de rede, o modelo é segmentado em diferentes partes que podem ser executadas simultaneamente em nós diferentes, e cada uma delas será executada nos mesmos dados. A escalabilidade desse método depende do grau de paralelização de tarefas do algoritmo e é mais complexa de implementar do que o paralelismo de dados. 

No paralelismo de modelo, os nós de trabalho precisam apenas sincronizar os parâmetros compartilhados, geralmente uma vez para cada etapa de propagação direta ou retroativa. Além disso, os modelos maiores não são uma preocupação, pois cada nó opera em uma subseção do modelo nos mesmos dados de treinamento.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md) com o SDK do Python.
* Para obter um exemplo técnico, consulte o [cenário de arquitetura de referência](/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Treinar modelos de ml com TensorFlow](how-to-train-tensorflow.md).
* [Treinar modelos de ml com PyTorch](how-to-train-pytorch.md).