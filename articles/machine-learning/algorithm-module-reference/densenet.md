---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo DenseNet no designer de Azure Machine Learning para criar um modelo de classificação de imagem usando o algoritmo DenseNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: d21c7443f6b30d0b7d6e8295c0c9b060a80d9a62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421321"
---
# <a name="densenet"></a>DenseNet

Este artigo descreve como usar o módulo **DenseNet** no designer de Azure Machine Learning para criar um modelo de classificação de imagem usando o algoritmo DenseNet.  

Esse algoritmo de classificação é um método de aprendizado supervisionado e requer um diretório de imagem rotulado. 

> [!NOTE]
> Este módulo não dá suporte a conjuntos de dados rotulados gerados a partir de *Rótulos de dado* no estúdio, mas só dá suporte ao diretório de imagem rotulado gerado do módulo [converter para diretório de imagem](convert-to-image-directory.md) . 

Você pode treinar o modelo fornecendo o modelo e o diretório de imagem rotulado como entradas para [treinar o modelo Pytorch](train-pytorch-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada usando o [modelo de imagem de Pontuação](score-image-model.md).

### <a name="more-about-densenet"></a>Mais sobre DenseNet

Para obter mais informações sobre o DenseNet, consulte o artigo de pesquisa, redes de confirmações [conectadas densamente](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>Como configurar o DenseNet

1.  Adicione o módulo **DenseNet** ao seu pipeline no designer.  

2.  Para **nome do modelo**, especifique o nome de uma determinada estrutura DenseNet e você pode selecionar entre DenseNet com suporte: ' densenet121 ', ' densenet161 ', ' densenet169 ', ' densenet201 '.

3.  Para pré- **treinado**, especifique se deseja usar um modelo previamente treinado em ImageNet. Se selecionado, você pode ajustar o modelo com base no modelo pretreinado selecionado; Se estiver desmarcada, você poderá treinar do zero.

4.  Para a **eficiência da memória**, especifique se deseja usar o ponto de verificação, o que é muito mais eficiente na memória, mas mais lento. Para obter mais informações, consulte o artigo de pesquisa, [implementação com eficiência de memória de DenseNets](https://arxiv.org/pdf/1707.06990.pdf).

5.  Conecte a saída do módulo **DenseNet** de conjunto de módulos, treinamento e imagem de validação ao [modelo Train Pytorch](train-pytorch-model.md). 

6. Enviar o pipeline.


## <a name="results"></a>Resultados

Após a execução do pipeline ser concluída, para usar o modelo de pontuação, conecte o [modelo Train Pytorch](train-pytorch-model.md) ao [modelo de imagem de Pontuação](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Observações técnicas  

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Nome             | Intervalo | Type    | Padrão     | Descrição                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nome do modelo       | Qualquer   | Modo    | densenet201 | Nome de uma determinada estrutura DenseNet     |
| Pré-treinados       | Qualquer   | Booliano | verdadeiro        | Se deve ser usado um modelo previamente treinado em ImageNet |
| Com eficiência de memória | Qualquer   | Booliano | Falso       | Se deseja usar o ponto de verificação, o que é muito mais eficiente na memória, mas mais lento |

###  <a name="output"></a>Saída  

| Nome            | Type                    | Descrição                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo não treinado | UntrainedModelDirectory | Um modelo de DenseNet não treinado que pode ser conectado ao modelo Train Pytorch. |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
