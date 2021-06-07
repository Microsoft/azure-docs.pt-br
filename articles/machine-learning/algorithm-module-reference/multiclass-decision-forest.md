---
title: 'Floresta de decisão multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo floresta de decisão multiclasse no Azure Machine Learning para criar um modelo de aprendizado de máquina com base no algoritmo de *floresta de decisão* .
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 1be66bdd8a1cf25a32ad3102d770078c904c4b6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94376818"
---
# <a name="multiclass-decision-forest-module"></a>Módulo de floresta de decisão multiclasse

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de aprendizado de máquina com base no algoritmo de *floresta de decisão* . Uma floresta de decisão é um modelo Ensemble que cria rapidamente uma série de árvores de decisão e, ao mesmo tempo, aprende com dados marcados.

## <a name="more-about-decision-forests"></a>Mais sobre as florestas de decisão

O algoritmo de floresta de decisão é um método de aprendizado Ensemble para classificação. O algoritmo funciona criando várias árvores de decisão e, em seguida, *votando* a classe de saída mais popular. A votação é uma forma de agregação, na qual cada árvore em uma floresta de decisão de classificação gera um histograma de frequência não normalizado de rótulos. O processo de agregação soma esses histogramas e normaliza o resultado para obter as "probabilidades" para cada rótulo. As árvores que têm alta confiança de previsão têm um peso maior na decisão final do Ensemble.

As árvores de decisão em geral são modelos não paramétricos, o que significa que dão suporte a dados com distribuições variadas. Em cada árvore, uma sequência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó folha (decisão) seja atingido.

Árvores de decisão têm muitas vantagens:

+ Podem representar limites de decisão não lineares.
+ São eficientes no uso de memória e computação durante o treinamento e a previsão.
+ Elas executam a seleção de recursos integrados e classificação.
+ Elas são flexíveis na presença de recursos com ruídos.

O classificador de floresta de decisão em Azure Machine Learning consiste em um Ensemble de árvores de decisão. Em termos gerais, os modelos de ensemble fornecem melhor cobertura e precisão que árvores de decisão únicas. Para obter mais informações, consulte [árvores de decisão](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a floresta de decisão multiclasse

1. Adicione o módulo **floresta de decisão multiclasse** ao seu pipeline no designer. Você pode encontrar esse módulo em **Machine Learning**, **inicializar modelo** e **classificação**.

2. Clique duas vezes no módulo para abrir o painel **Propriedades** .

3. Para o **método de reamostragem**, escolha o método usado para criar as árvores individuais.  Você pode escolher entre bagging ou replicação.

    + **Bagging**: bagging também é chamado de *agregação de Bootstrap*. Nesse método, cada árvore é expandida em um novo exemplo, criada pela amostragem aleatória do conjunto de datas original com substituição, até que você tenha um conjunto de um DataSet com o tamanho original. As saídas dos modelos são combinadas por *votação*, que é uma forma de agregação. Para obter mais informações, consulte a entrada da Wikipédia para agregação de bootstrap.

    + **Replicate**: na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado de divisão é usado para cada nó de árvore permanece aleatório, criando árvores diversas.

   

4. Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .

    + **Parâmetro único**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto de valores como argumentos.

    + **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais.   

5. **Número de árvores de decisão**: digite o número máximo de árvores de decisão que podem ser criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento pode aumentar.

    Se você definir o valor como 1; no entanto, isso significa que apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.

6. **Profundidade máxima das árvores de decisão**: digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, mas você corre o risco de algum tempo de treinamento com overfitting e maior.

7. **Número de divisões aleatórias por nó**: digite o número de divisões a serem usadas ao compilar cada nó da árvore. Uma *divisão* significa que os recursos em cada nível da árvore (nó) são divididos aleatoriamente.

8. **Número mínimo de amostras por nó folha**: indique o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore. Aumentando esse valor, você aumenta o limite para a criação de novas regras.

    Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.



10. Conecte um DataSet rotulado e treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros**, conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.

11. Envie o pipeline.



## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 