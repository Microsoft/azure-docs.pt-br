---
title: 'Regressão logística Two-Class: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão logística Two-Class no Azure Machine Learning para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2e29a666f4d478e11986f834cff94d9743223f22
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012639"
---
# <a name="two-class-logistic-regression-module"></a>Módulo de regressão logística Two-Class

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados. 

A regressão logística é uma técnica estatística conhecida que é usada para modelar muitos tipos de problemas. Esse algoritmo é um método de *aprendizado supervisionado* ;  Portanto, você deve fornecer um conjunto de um DataSet que já contém os resultados para treinar o modelo.  

### <a name="about-logistic-regression"></a>Sobre a regressão logística  

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um resultado e é especialmente popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento ajustando dados a uma função logística.
  
Neste módulo, o algoritmo de classificação é otimizado para dicotômicas ou variáveis binárias. Se você precisar classificar vários resultados, use o módulo [regressão logística multiclasse](./multiclass-logistic-regression.md) .

##  <a name="how-to-configure"></a>Como configurar  

Para treinar esse modelo, você deve fornecer um conjunto de um DataSet que contenha um rótulo ou uma coluna de classe. Como esse módulo destina-se a problemas de duas classes, o rótulo ou a coluna de classe deve conter exatamente dois valores. 

Por exemplo, a coluna de rótulo pode ser [voto] com os valores possíveis de "Sim" ou "não". Ou, pode ser [risco de crédito], com os valores possíveis de "alto" ou "baixo". 
  
1.  Adicione o módulo **regressão logística de duas classes** ao seu pipeline.  
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.  

    -   **Intervalo de parâmetros**: se você não tiver certeza dos melhores parâmetros, poderá encontrar os parâmetros ideais usando o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) . Você fornece algum intervalo de valores, e o instrutor itera em várias combinações de configurações para determinar a combinação de valores que produz o melhor resultado.
  
3.  Para a **tolerância de otimização**, especifique um valor de limite a ser usado ao otimizar o modelo. Se a melhoria entre as iterações cair abaixo do limite especificado, o algoritmo será considerado convergido em uma solução e o treinamento será interrompido.  
  
4.  Para peso de **regularização L1** e **peso de regularização L2**, digite um valor a ser usado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.  
     A *regularização* é um método para evitar o superajuste por meio da penalização de modelos com valores de coeficiente extremo. A regularização funciona adicionando a penalidade associada aos valores de coeficiente ao erro da hipótese. Assim, um modelo preciso com valores de coeficiente extremo seria penalizado mais, mas um modelo menos preciso com valores mais conservadores seria penalizado menos.  
  
     As regularizações L1 e L2 têm efeitos e usos diferentes.  
  
    -   L1 pode ser aplicado aos modelos esparsos, que é útil ao trabalhar com dados de grande dimensão.  
  
    -   Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  
  
     Este algoritmo dá suporte a uma combinação linear de valores de regularização L1 e L2: ou seja, se <code>x = L1</code> e <code>y = L2</code> , <code>ax + by = c</code> define o intervalo linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre a regularização de L1 e L2? O artigo a seguir fornece uma discussão de como a regularização L1 e L2 é diferente e como elas afetam o ajuste de modelo, com exemplos de código para a regressão logística e modelos de rede neural: a  [regularização L1 e L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)  
    >
    > Combinações lineares diferentes de termos de L1 e L2 foram desenvolvedas para modelos de regressão logística: por exemplo, [regularização de rede elástica](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerimos que você referencie essas combinações para definir uma combinação linear que seja efetiva em seu modelo.
      
5.  Para o **tamanho da memória para l-BFGS**, especifique a quantidade de memória a ser usada para a otimização de *l-BFGS* .  
  
     L-BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é conhecido por estimativa de parâmetro. Esse parâmetro indica o número de posições anteriores e gradientes a armazenar o cálculo da próxima etapa.  
  
     Esse parâmetro de otimização limita a quantidade de memória usada para calcular a próxima etapa e direção. Quando você especifica menos memória, o treinamento é mais rápido, mas menos preciso.  
  
6.  Para **semente de número aleatório**, digite um valor inteiro. Definir um valor de semente é importante se você quiser que os resultados sejam reproduzidos em várias execuções do mesmo pipeline.  
  
  
8. Adicione um DataSet rotulado ao pipeline e treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros**, conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.  
  
9. Enviar o pipeline.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:
 
  
+ Para fazer previsões sobre novos dados, use o modelo treinado e novos dados como entrada para o módulo [modelo de Pontuação](./score-model.md) . 


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.