---
title: Treinar modelo de Pytorch
titleSuffix: Azure Machine Learning
description: Use o módulo treinar modelos do Pytorch no designer de Azure Machine Learning para treinar modelos do zero ou ajustar os modelos existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420658"
---
# <a name="train-pytorch-model"></a>Treinar modelo de Pytorch

Este artigo descreve como usar o módulo de **modelo Train Pytorch** no designer de Azure Machine Learning para treinar modelos Pytorch como DenseNet. O treinamento ocorre depois que você define um modelo e define seus parâmetros e requer dados rotulados. 

## <a name="how-to-use-train-pytorch-model"></a>Como usar o modelo Train Pytorch 

1. Adicione o módulo [DenseNet](densenet.md) ou [ResNet](resnet.md) ao seu rascunho de pipeline no designer.

2. Adicione o módulo **modelo de treinamento Pytorch** ao pipeline. Você pode encontrar esse módulo na categoria **treinamento do modelo** . Expanda **treinar** e arraste o módulo **modelo de treinamento Pytorch** para seu pipeline.

   > [!NOTE]
   > O módulo de **modelo Train Pytorch** é melhor executado na computação de tipo **GPU** para um grande conjunto de grandes, caso contrário, o pipeline falhará. Você pode selecionar computação para um módulo específico no painel direito do módulo Configurando **usar outro destino de computação**.

3.  Na entrada à esquerda, anexe um modelo não treinado. Anexe o DataSet de treinamento e o conjunto de dados de validação à entrada do meio e do lado direito do **modelo Train Pytorch**.

    Para o modelo não treinado, ele deve ser um modelo pytorch como DenseNet; caso contrário, um ' InvalidModelDirectoryError ' será lançado.

    Para o conjunto de um, o conjunto de e de treinamento deve ser um diretório de imagem rotulado. Consulte **converter em diretório de imagem** para saber como obter um diretório de imagem rotulado. Se não for rotulado, um ' NotLabeledDatasetError ' será lançado.

    O conjunto de e-DataSet de treinamento têm as mesmas categorias de rótulo, caso contrário, um InvalidDatasetError será gerado.

4.  Para **épocas** , especifique quantas épocas você gostaria de treinar. O conjunto de um inteiro será iterado em todas as épocas, por padrão, 5.

5.  Em **tamanho do lote** , especifique quantas instâncias deseja treinar em um lote, por padrão, 16.

6.  Para **taxa de aprendizagem** , especifique um valor para a *taxa de aprendizado*. Os valores de taxa de aprendizagem controlam o tamanho da etapa usada no otimizador como SGD cada vez que o modelo é testado e corrigido.

    Ao tornar a taxa menor, você testa o modelo com mais frequência, com o risco que você pode ficar preso em um limite local. Aumentando a etapa, você pode convergir mais rapidamente, com o risco mínimo de errar o alvo verdadeiro. Por padrão, 0, 1.

7.  Para **semente aleatória** , opcionalmente, digite um valor inteiro para usar como a semente. Usar uma semente é recomendado se você quiser garantir reprodução do experimento entre execuções.

8.  Para **paciência** , especifique quantas épocas para interromper o treinamento antecipado se a perda de validação não diminuir consecutivamente. Por padrão 3.

9.  Envie o pipeline. Se o seu conjunto de seus conjuntos de seu tamanho for maior, levará um tempo e a computação da GPU será recomendada.

## <a name="results"></a>Resultados

Após a execução do pipeline ser concluída, para usar o modelo de pontuação, conecte o [modelo Train Pytorch](train-pytorch-model.md) ao [modelo de imagem de Pontuação](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Observações técnicas
###  <a name="expected-inputs"></a>Entradas esperadas  

| Nome               | Tipo                    | Descrição                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Modelo não treinado    | UntrainedModelDirectory | Modelo não treinado, exigir pytorch         |
| Conjunto de dados de treinamento   | ImageDirectory          | Conjunto de dados de treinamento                         |
| Conjunto de dados de validação | ImageDirectory          | Conjunto de validação para avaliação a cada época |

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Name          | Intervalo            | Type    | Padrão | Descrição                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Épocas        | >0               | Integer | 5       | Selecione a coluna que contém a coluna de rótulo ou de resultado |
| Tamanho do lote    | >0               | Integer | 16      | Quantas instâncias para treinar em um lote   |
| Taxa de aprendizado | >= double.Épsilon | Float   | 0,001   | A taxa de aprendizagem inicial do otimizador Descendente do gradiente estocástico. |
| Propagação aleatória   | Qualquer              | Integer | 1       | A propagação para o gerador de número aleatório usado pelo modelo. |
| Paciência      | >0               | Integer | 3       | Quantas épocas para o treinamento de parada antecipada   |

###  <a name="outputs"></a>Saídas  

| Nome          | Tipo           | Descrição   |
| ------------- | -------------- | ------------- |
| Modelo treinado | ModelDirectory | Modelo treinado |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 



