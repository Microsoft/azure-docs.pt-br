---
title: 'Regressão de floresta de decisão: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de regressão de floresta de decisão em Azure Machine Learning para criar um modelo de regressão com base em uma Ensemble de árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 76fd09b5e2c2540cbc1608558800e7897a6cf839
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375349"
---
# <a name="decision-forest-regression-module"></a>Módulo de regressão de floresta de decisão

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de regressão com base em uma Ensemble de árvores de decisão.

Depois de configurar o modelo, você deve treinar o modelo usando um DataSet rotulado e o módulo [treinar modelo](./train-model.md) . O modelo treinado pode então ser usado para fazer previsões. 

## <a name="how-it-works"></a>Como ele funciona

Árvores de decisão são modelos não paramétricos que executam uma sequência de testes simples para cada instância, percorrendo uma estrutura de dados de árvore binária até que um nó folha (decisão) seja atingido.

As árvores de decisão têm estas vantagens:

- São eficientes no uso de memória e computação durante o treinamento e a previsão.

- Podem representar limites de decisão não lineares.

- Realizam seleção e classificação de recursos integrados e são resilientes na presença de recursos com ruídos.

Esse modelo de regressão consiste de um ensemble de árvores de decisão. Cada árvore em uma floresta de decisão de regressão gera uma distribuição gaussiana como uma previsão. Uma agregação é executada sobre o Ensemble de árvores para encontrar uma distribuição gaussiana mais próxima da distribuição combinada para todas as árvores no modelo.

Para obter mais informações sobre a estrutura teórica para este algoritmo e sua implementação, consulte este artigo: [florestas de decisão: uma estrutura unificada para classificação, regressão, estimativa de densidade, aprendizado de diversa e aprendizado de Semi-Supervised](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar o modelo de regressão de floresta de decisão

1. Adicione o módulo **regressão de floresta de decisão** ao pipeline. Você pode encontrar o módulo no designer em **Machine Learning** , **inicializar modelo** e **regressão**.

2. Abra as propriedades do módulo e, para o **método de reamostragem** , escolha o método usado para criar as árvores individuais.  Você pode escolher entre **bagging** ou **replicar**.

    - **Bagging** : bagging também é chamado de *agregação de Bootstrap*. Cada árvore em uma floresta de decisão de regressão gera uma distribuição gaussiana por meio de previsão. A agregação é para localizar um gaussiano cujos primeiros dois momentos correspondem ao momento da mistura de distribuições gaussianas dadas pela combinação de todas as distribuições retornadas por árvores individuais.

         Para obter mais informações, consulte a entrada da Wikipédia para [agregação de Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicate** : na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado de divisão é usado para cada nó de árvore permanece aleatório e as árvores serão diversificadas.

         Para obter mais informações sobre o processo de treinamento com a opção **replicar** , consulte [florestas de decisão para pesquisa Visual computacional e análise de imagem médica. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .

    - **Parâmetro único**

      Se você souber como deseja configurar o modelo, é possível fornecer um conjunto específico de valores como argumentos. Você pode ter aprendido esses valores por experimentação ou os recebido como orientação.

    - **Intervalo de parâmetros** : Selecione esta opção se você não tiver certeza dos melhores parâmetros e quiser executar uma limpeza de parâmetro. Selecione um intervalo de valores para iteração e os [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md) iteram sobre todas as combinações possíveis das configurações que você forneceu para determinar os hiperparâmetros que produzem os resultados ideais. 



4. Para o **número de árvores de decisão** , indique o número total de árvores de decisão a serem criadas no Ensemble. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumentará o tempo de treinamento.

    > [!TIP]
    > Se você definir o valor como 1; no entanto, isso significa que apenas uma árvore será produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional será executada.

5. Para obter a **profundidade máxima das árvores de decisão** , digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, mas você corre o risco de algum tempo de treinamento com overfitting e maior.

6. Para o **número de divisões aleatórias por nó** , digite o número de divisões a serem usadas ao compilar cada nó da árvore. Uma *divisão* significa que os recursos em cada nível da árvore (nó) são divididos aleatoriamente.

7. Para o **número mínimo de amostras por nó folha** , indique o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.

     Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.


9. Treine o modelo:

    + Se você definir **criar modo de instrutor** como um **único parâmetro** , conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](train-model.md) .  
  
    + Se você definir **criar modo de instrutor** como **intervalo de parâmetros** , conecte um conjunto de um DataSet marcado e treine o modelo usando [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o valor padrão na lista de parâmetros únicos.  
    > 
    > Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.  
    > 
    > Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.

   

10. Enviar o pipeline.

### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione o módulo de treinamento e, em seguida, alterne para a guia **saídas** no painel direito. Clique no ícone **registrar modelo**.  Você pode encontrar o modelo salvo como um módulo na árvore de módulo. 

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 