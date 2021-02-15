---
title: 'Regressão rápida de Quantil de floresta: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão rápida de Quantil de floresta para criar um modelo de regressão que pode prever valores para um número especificado de quantis.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907949"
---
# <a name="fast-forest-quantile-regression"></a>Regressão rápida de quantil de floresta

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de regressão de Quantil de floresta rápida em um pipeline. A regressão rápida de Quantil de floresta é útil se você deseja entender mais sobre a distribuição do valor previsto, em vez de obter um único valor de previsão média. Esse método tem muitos aplicativos, incluindo:  
  
- Previsão os preços  
  
- Estimativa do desempenho do aluno ou aplicação de gráficos de crescimento para avaliar o desenvolvimento do filho  
  
- Descobrir relações de previsão em casos onde há apenas uma relação fraca entre variáveis  
  
Esse algoritmo de regressão é um método de aprendizado **supervisionado** , o que significa que ele requer um conjunto de informações marcado que inclui uma coluna de rótulo. Como é um algoritmo de regressão, a coluna de rótulo deve conter apenas valores numéricos.

## <a name="more-about-quantile-regression"></a>Mais informações sobre regressão de Quantil

Há muitos tipos diferentes de regressão. Simplificando, a regressão significa ajustar um modelo a um destino expresso como um vetor numérico. No entanto, os estatísticos têm desenvolvido métodos cada vez mais avançados de regressão.

A definição mais simples de *Quantil* é um valor que divide um conjunto de dados em grupos de tamanho igual; assim, os valores de Quantil marcam os limites entre os grupos. Estatisticamente, quantis são valores obtidos em intervalos regulares do inverso da função de distribuição cumulativa (CDF) de uma variável aleatória.

Enquanto os modelos de regressão linear tentam prever o valor de uma variável numérica usando uma única estimativa, a *média*, às vezes, você precisa prever o intervalo ou toda a distribuição da variável de destino. Técnicas como regressão de Bayesiana e regressão de Quantil foram desenvolvidas para essa finalidade.

A regressão de Quantil ajuda a entender a distribuição do valor previsto. Os modelos de regressão de quantil em forma de árvore, como o usado neste módulo, têm a vantagem adicional de poderem ser usados para prever distribuições não paramétricas.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Como configurar a regressão de Quantil de floresta rápida

1. Adicione o módulo **regressão rápida de Quantil de floresta** ao seu pipeline no designer. Você pode encontrar esse módulo em **algoritmos de Machine Learning**, na categoria **regressão** .

2. No painel direito do módulo **regressão rápida de Quantil de floresta** , especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    - **Parâmetro único**: se você souber como deseja configurar o modelo, forneça um conjunto específico de valores como argumentos. Ao treinar o modelo, use [treinar modelo](train-model.md).
  
    - **Intervalo de parâmetros**: se você não tiver certeza dos melhores parâmetros, faça uma limpeza de parâmetro usando o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) . O instrutor itera em vários valores que você especifica para encontrar a configuração ideal.

3. **Número de árvores**, digite o número máximo de árvores que podem ser criadas no Ensemble. Quando cria mais árvores, isso geralmente leva a maior precisão, mas a mais tempo de treinamento.  

4. **Número de folhas**, digite o número máximo de folhas ou nós de terminal que podem ser criados em qualquer árvore.  

5. **Número mínimo de instâncias de treinamento necessárias para formar uma folha**, especifique o número mínimo de exemplos necessários para criar qualquer nó de terminal (folha) em uma árvore.  
  
     Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos 5 casos que atendem as mesmas condições.

6. **Fração bagging**, especifique um número entre 0 e 1 que represente a fração de amostras a ser usada ao criar cada grupo de quantis. Os exemplos são escolhidos aleatoriamente, com substituição.

7. **Dividir fração**, digite um número entre 0 e 1 que represente a fração de recursos a serem usados em cada divisão da árvore. Os recursos usados sempre são escolhidos aleatoriamente.

8. **Quantis a ser estimado**, digite uma lista separada por ponto-e-vírgula do quantis para o qual você deseja que o modelo treine e crie previsões.
  
     Por exemplo, se você quiser criar um modelo que estima os quartils, digite `0.25; 0.5; 0.75` .  

9. Opcionalmente, digite um valor para a **semente de número aleatório** para propagar o gerador de número aleatório usado pelo modelo.  O padrão é 0, que significa que uma semente aleatória é escolhida.
  
     Você deve fornecer um valor se precisar reproduzir os resultados entre execuções sucessivas nos mesmos dados.  

10. Conecte o conjunto de e-o de treinamento e o modelo não treinado a um dos módulos de treinamento: 

    - Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](train-model.md) .

    - Se você definir **criar modo de instrutor** como **intervalo de parâmetros**, use o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o primeiro valor na lista intervalo de parâmetros.
    > 
    > - Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.
    > 
    > - Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.

11. Enviar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione o módulo de treinamento e, em seguida, vá para a guia **saídas + logs** no painel direito. Clique no ícone **registrar conjunto de registros**.  Você pode encontrar o modelo salvo como um módulo na árvore de módulo.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.
