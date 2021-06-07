---
title: 'Two-Class média de perceptron: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Two-Class Averageed perceptron no designer para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421134"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class módulo de média de perceptron

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de aprendizado de máquina com base no algoritmo médio de perceptron.  
  
Esse algoritmo de classificação é um método de aprendizado supervisionado e requer um conjunto de informações *marcado*, que inclui uma coluna de rótulo. Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar o modelo](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

### <a name="about-averaged-perceptron-models"></a>Sobre os modelos de média de perceptron

O *Método Average perceptron* é uma versão inicial e simples de uma rede neural. Nessa abordagem, as entradas são classificadas em várias saídas possíveis com base em uma função linear e, em seguida, combinadas com um conjunto de pesos que são derivados do vetor de recurso — por isso o nome "perceptron".

Os modelos perceptron mais simples são adequados ao aprendizado de padrões separáveis linearmente, enquanto as redes neurais (especialmente redes neurais profundas) podem modelar limites de classe mais complexos. No entanto, os perceptrons são mais rápidos e conforme eles processem casos em série, podem ser usados com treinamento contínuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar Two-Class médias de perceptron

1.  Adicione o módulo **perceptron médio de duas classes** ao seu pipeline.  

2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: se você souber como deseja configurar o modelo, forneça um conjunto específico de valores como argumentos.

    -   **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais.  
  
3.  Para **taxa de aprendizagem**, especifique um valor para a *taxa de aprendizado*. Os valores de taxa de aprendizagem controlam o tamanho da etapa usada em estocástico Grad descendente cada vez que o modelo é testado e corrigido.
  
     Ao tornar a taxa menor, você testa o modelo com mais frequência, com o risco que você pode ficar preso em um limite local. Aumentando a etapa, você pode convergir mais rapidamente, com o risco mínimo de errar o alvo verdadeiro.
  
4.  Para **número máximo de iterações**, digite o número de vezes que você deseja que o algoritmo examine os dados de treinamento.  
  
     Parando no início geralmente fornece melhor generalização. Aumentar o número de iterações melhora o ajuste, com o risco de superajuste.
  
5.  Para **semente de número aleatório**, opcionalmente, digite um valor inteiro para usar como a semente. Usar uma semente é recomendado se você quiser garantir reprodução do pipeline entre execuções.  
  
1.  Conecte-se a um conjunto de um de treinamento e treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros**, conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.




## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 