---
title: ResNet
titleSuffix: Azure Machine Learning
description: Saiba como criar um modelo de classificação de imagem no designer de Azure Machine Learning usando o algoritmo ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 88a820d0f1fa9515b4f2992a8305a2d1065e0987
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421202"
---
# <a name="resnet"></a>ResNet

Este artigo descreve como usar o módulo **ResNet** no designer de Azure Machine Learning para criar um modelo de classificação de imagem usando o algoritmo ResNet.  

Esse algoritmo de classificação é um método de aprendizado supervisionado e requer um conjunto de informações rotulado. 
> [!NOTE]
> Este módulo não dá suporte a conjuntos de dados rotulados gerados a partir de *Rótulos de dado* no estúdio, mas só dá suporte ao diretório de imagem rotulado gerado do módulo [converter para diretório de imagem](convert-to-image-directory.md) . 

Você pode treinar o modelo fornecendo um modelo e um diretório de imagem rotulado como entradas para [treinar o modelo Pytorch](train-pytorch-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada usando o [modelo de imagem de Pontuação](score-image-model.md).

### <a name="more-about-resnet"></a>Mais sobre ResNet

Consulte [Este artigo](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) para obter mais detalhes sobre o ResNet.

## <a name="how-to-configure-resnet"></a>Como configurar o ResNet

1.  Adicione o módulo **ResNet** ao seu pipeline no designer.  

2.  Para **nome do modelo** , especifique o nome de uma determinada estrutura ResNet e você pode selecionar entre ResNet com suporte: ' resnet18 ', ' resnet34 ', ' resnet50 ', ' resnet101 ', ' resnet152 ', ' resnet152 ', ' resnext50 \_ 32x4d ', ' resnext101 \_ 32x8d ', ' wide_resnet50 \_ 2 ', ' wide_resnet101 \_ 2 '.

3.  Para pré- **treinado** , especifique se deseja usar um modelo previamente treinado em ImageNet. Se selecionado, você pode ajustar o modelo com base no modelo pretreinado selecionado; Se estiver desmarcada, você poderá treinar do zero.

4.  Conecte a saída do módulo **DenseNet** módulo do conjunto de módulos de imagem, treinamento e validação ao [modelo Train Pytorch](train-pytorch-model.md). 

5. Enviar o pipeline.

## <a name="results"></a>Resultados

Após a execução do pipeline ser concluída, para usar o modelo de pontuação, conecte o [modelo Train Pytorch](train-pytorch-model.md) ao [modelo de imagem de Pontuação](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Observações técnicas  

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Name       | Intervalo | Type    | Padrão           | Descrição                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Nome do modelo | Qualquer   | Mode    | resnext101 \_ 32x8d | Nome de uma determinada estrutura ResNet       |
| Pré-treinados | Qualquer   | Booliano | verdadeiro              | Se deve ser usado um modelo previamente treinado em ImageNet |
|            |       |         |                   |                                          |

###  <a name="output"></a>Saída  

| Nome            | Tipo                    | Descrição                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo não treinado | UntrainedModelDirectory | Um modelo de ResNet não treinado que pode ser conectado ao modelo Train Pytorch. |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 