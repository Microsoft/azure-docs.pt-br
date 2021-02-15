---
title: 'Floresta de decisão de Two-Class: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de floresta de decisão Two-Class no Azure Machine Learning para criar um modelo de aprendizado de máquina com base no algoritmo de florestas de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/03/2020
ms.openlocfilehash: c4c303a7640454ba0cb6622b21fd161354266068
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375902"
---
# <a name="two-class-decision-forest-module"></a>Módulo de floresta de decisão Two-Class

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de aprendizado de máquina com base no algoritmo florestas de decisão.  

As florestas de decisão são modelos de Ensemble rápidos e supervisionados. Esse módulo é uma boa opção se você quiser prever um destino com um máximo de dois resultados. 

## <a name="understanding-decision-forests"></a>Noções básicas sobre florestas de decisão

Esse algoritmo de floresta de decisão é um método de aprendizado Ensemble destinado a tarefas de classificação. Os métodos Ensemble são baseados no princípio geral que, em vez de depender de um único modelo, você pode obter resultados melhores e um modelo mais generalizado criando vários modelos relacionados e combinando-os de alguma forma. Em termos gerais, os modelos de ensemble fornecem melhor cobertura e precisão que árvores de decisão únicas. 

Há várias maneiras de criar modelos individuais e combiná-los em um Ensemble. Essa implementação específica de uma floresta de decisão funciona criando várias árvores de decisão e, em seguida, **votando** a classe de saída mais popular. A votação é um dos métodos mais conhecidos para gerar resultados em um modelo Ensemble. 

+ Muitas árvores de classificação individuais são criadas, usando o conjunto de um inteiro, mas pontos de partida diferentes (geralmente aleatórios). Isso difere da abordagem de floresta aleatória, na qual as árvores de decisão individuais podem usar apenas uma parte aleatória dos dados ou recursos.
+ Cada árvore na árvore da floresta de decisão gera um histograma de frequência não normalizado dos rótulos. 
+ O processo de agregação soma esses histogramas e normaliza o resultado para obter as "probabilidades" para cada rótulo. 
+ As árvores que têm alta confiança de previsão terão um peso maior na decisão final do Ensemble.

As árvores de decisão em geral têm muitas vantagens para tarefas de classificação:
  
- Eles podem capturar limites de decisão não lineares.
- Você pode treinar e prever muitos dados, pois eles são eficientes no uso de memória e computação.
- A seleção de recursos é integrada nos processos de treinamento e classificação.  
- As árvores podem acomodar dados com ruído e muitos recursos.  
- Eles são modelos não paramétricos, o que significa que podem manipular dados com distribuições variadas. 

No entanto, as árvores de decisão simples podem sobreajustar dados e são menos generalizadas do que a árvore conjuntos.

Para obter mais informações, consulte [florestas de decisão](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Como configurar
  
1.  Adicione o módulo **floresta de decisão de duas classes** ao seu pipeline no Azure Machine Learning e abra o painel **Propriedades** do módulo. 

    Você pode encontrar o módulo em **Machine Learning**. Expanda **inicializar** e, em seguida, **classificação**.  
  
2.  Para o **método de reamostragem** , escolha o método usado para criar as árvores individuais.  Você pode escolher entre **bagging** ou **replicar**.  
  
    -   **Bagging** : bagging também é chamado de *agregação de Bootstrap*. Nesse método, cada árvore é expandida em um novo exemplo, criada pela amostragem aleatória do conjunto de datas original com substituição, até que você tenha um conjunto de um DataSet com o tamanho original.  
  
         As saídas dos modelos são combinadas por *votação* , que é uma forma de agregação. Cada árvore em uma floresta de decisão de classificação gera um histograma de frequência não normalizado de rótulos. A agregação é somar esses histogramas e normalizar para obter as "probabilidades" para cada rótulo. Dessa forma, as árvores que têm alta confiança de previsão terão um peso maior na decisão final do Ensemble.  
  
         Para obter mais informações, consulte a entrada da Wikipédia para agregação de bootstrap.  
  
    -   **Replicate** : na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado de divisão é usado para cada nó de árvore permanece aleatório e as árvores serão diversificadas.   
  
3.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único** : se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.

    -   **Intervalo de parâmetros** : se você não tiver certeza dos melhores parâmetros, poderá encontrar os parâmetros ideais usando o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) . Você fornece algum intervalo de valores, e o instrutor itera em várias combinações de configurações para determinar a combinação de valores que produz o melhor resultado.
  
4.  Para **número de árvores de decisão** , digite o número máximo de árvores de decisão que podem ser criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumenta.  
  
    > [!NOTE]
    >  Se você definir o valor como 1. No entanto, apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.
  
5.  Para obter a **profundidade máxima das árvores de decisão** , digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, mas você corre o risco de algum tempo de treinamento com overfitting e maior.
  
  
7.  Para o **número mínimo de amostras por nó folha** , indique o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.
  
     Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.  
  
8.  Selecione a opção **permitir valores desconhecidos para recursos categóricos** para criar um grupo de valores desconhecidos nos conjuntos de treinamento ou validação. O modelo pode ser menos preciso para valores conhecidos, mas pode fornecer previsões melhores para valores novos (desconhecidos). 

     Se você desmarcar essa opção, o modelo poderá aceitar somente os valores contidos nos dados de treinamento.
  
9. Anexe um DataSet rotulado e treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro** , conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros** , conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.  
    
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo de pontuação, adicione o módulo **modelo de Pontuação** a um pipeline.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 