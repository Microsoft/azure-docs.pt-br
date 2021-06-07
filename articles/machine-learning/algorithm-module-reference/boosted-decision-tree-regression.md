---
title: 'Regressão de árvore de decisão aumentada: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão da árvore de decisão aumentada no Azure Machine Learning para criar um Ensemble de árvores de regressão usando o aumento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375322"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão da árvore de decisão aumentada

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um Ensemble de árvores de regressão usando o aumento. O *aumento* significa que cada árvore depende de árvores anteriores. O algoritmo aprende ajustando o resíduo das árvores que o precedem. Assim, impulsionada em um ensemble de árvore de decisão tende a aumentar a precisão com um pequeno risco de menos cobertura.  

Este módulo é baseado no algoritmo LightGBM.
  
Esse método de regressão é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações *rotulado*. A coluna de rótulo deve conter valores numéricos.  

> [!NOTE]
> Use esse módulo somente com conjuntos de dados que usam variáveis numéricas.  

Depois de definir o modelo, treine-o usando o [modelo de treinamento](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Mais sobre árvores de regressão aumentadas  

O impulsionamento é um dos vários métodos clássicos para criar modelos ensemble, juntamente com bagging, florestas aleatórias e assim por diante.  Em Azure Machine Learning, as árvores de decisão aumentadas usam uma implementação eficiente do algoritmo de aumento de gradiente do MART. O aumento de gradiente é uma técnica de aprendizado de máquina para problemas de regressão. Ele cria cada árvore de regressão por etapas, usando uma função de perda predefinida para medir o erro em cada etapa e corrigi-lo na próxima. Portanto, o modelo de previsão é realmente um ensemble de modelos de previsão mais fracos.  
  
Em problemas de regressão, o aumento cria uma série de árvores de uma maneira passo e, em seguida, seleciona a árvore ideal usando uma função de perda diferenciável arbitrária.  
  
Para obter mais informações, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipédia sobre o aumento de gradiente fornece alguma experiência em árvores aumentadas. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: de RankNet para LambdaRank para LambdaMART: uma visão geral. Por J.C. Burges.

O método de impulsionamento de gradiente também pode ser usado para problemas de classificação, reduzindo-os à regressão com uma função de perda adequada. Para obter mais informações sobre a implementação de árvores aumentadas para tarefas de classificação, consulte [árvore de decisão aumentada de duas classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão da árvore de decisão aumentada

1.  Adicione o módulo **árvore de decisão aumentada** ao seu pipeline. Você pode encontrar esse módulo em **Machine Learning**, **inicializar**, na categoria **regressão** . 
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos. 
     
    -   **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais.    
   
  
3. **Número máximo de folhas por árvore**: indica o número máximo de nós de terminal (folhas) que podem ser criados em qualquer árvore.  

    Aumentando este valor, você aumenta potencialmente o tamanho da árvore e obtém maior precisão, com o risco de sobreajuste e tempo de treinamento maior.  

4. **Número mínimo de amostras por nó folha**: indique o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.

    Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos 5 casos que atendem as mesmas condições.

5. **Taxa de aprendizagem**: digite um número entre 0 e 1 que defina o tamanho da etapa durante o aprendizado. A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi na solução ideal. Se o tamanho da etapa for muito grande, você poderá exceder a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.

6. **Número de árvores construídas**: indica o número total de árvores de decisão a serem criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumenta.

    Se você definir o valor como 1; no entanto, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.

7. **Semente de número aleatório**: digite um inteiro não negativo opcional para usar como o valor de semente aleatória. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.

    Por padrão, a semente aleatória é definida como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.
  

9. Treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros**, conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.
    

10. Enviar o pipeline.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para usar o modelo de pontuação, conecte o [modelo de treinamento](train-model.md) ao modelo de [Pontuação](./score-model.md)para prever valores para novos exemplos de entrada.

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do **modelo treinado** e clique no ícone **registrar conjunto de registros** . A cópia do modelo treinado será salva como um módulo na árvore de módulo e não será atualizada em execuções sucessivas do pipeline.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
