---
title: 'Regressão Poisson: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão Poisson no designer de Azure Machine Learning para criar um modelo de regressão Poisson.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420692"
---
# <a name="poisson-regression"></a>Regressão de Poisson

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para criar um modelo de regressão Poisson em um pipeline. A regressão Poisson é destinada à previsão de valores numéricos, normalmente conta. Portanto, você deve usar este módulo para criar seu modelo de regressão somente se os valores que você está tentando prever se ajustarem às seguintes condições:

- A variável de resposta tem uma [distribuição Poisson](https://en.wikipedia.org/wiki/Poisson_distribution).  

- As contagens não podem ser negativas. O método falhará totalmente, se você tentar usá-lo com rótulos negativos.

- Uma distribuição Poisson é uma distribuição discreta; Portanto, não é significativo usar esse método com números não inteiros.

> [!TIP]
> Se o destino não for uma contagem, a regressão Poisson provavelmente não é um método apropriado. Tente [outros módulos de regressão no designer](./module-reference.md#machine-learning-algorithms). 

Depois de configurar o método de regressão, você deve treinar o modelo usando um conjunto de um DataSet que contém exemplos do valor que você deseja prever. O modelo treinado pode então ser usado para fazer previsões.

## <a name="more-about-poisson-regression"></a>Mais informações sobre a regressão Poisson

A regressão Poisson é um tipo especial de análise de regressão que é normalmente usado para contagens de modelos. Por exemplo, a regressão de Poisson seria útil nestes cenários:

- Modelar o número de resfriados associados a voos de avião

- Estimando o número de chamadas de serviço de emergência durante um evento

- Projetando o número de consultas de clientes subsequentes a uma promoção

- Criar tabelas de contingência

Como a variável de resposta tem uma distribuição Poisson, o modelo faz diferentes suposições sobre os dados e sua distribuição de probabilidade, por exemplo, regressão de mínimos quadrados. Portanto, os modelos Poisson devem ser interpretados de forma diferente de outros modelos de regressão.

## <a name="how-to-configure-poisson-regression"></a>Como configurar a regressão Poisson

1. Adicione o módulo **regressão Poisson** ao seu pipeline no designer. Você pode encontrar esse módulo em **algoritmos de Machine Learning** , na categoria **regressão** .

2. Adicione um DataSet que contém dados de treinamento do tipo correto. 

    Recomendamos que você use [Normalize data](normalize-data.md) para normalizar o conjunto de dados de entrada antes de usá-lo para treinar o regressor.

3. No painel direito do módulo **regressão Poisson** , especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    - **Parâmetro único** : se você souber como deseja configurar o modelo, forneça um conjunto específico de valores como argumentos.
  
    - **Intervalo de parâmetros** : se você não tiver certeza dos melhores parâmetros, faça uma limpeza de parâmetro usando o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) . O instrutor itera em vários valores que você especifica para encontrar a configuração ideal.
  
4. **Tolerância de otimização** : digite um valor que defina o intervalo de tolerância durante a otimização. Quanto menor o valor, mais lento e mais preciso o ajuste.

5. Peso de **regularização de L1** e **peso de regularização L2** : digite valores a serem usados para regularização de L1 e L2. A *Regularização* adiciona restrições ao algoritmo sobre aspectos do modelo que são independentes dos dados de treinamento. A regularização também é usada para evitar o sobreajuste. 

    - A regularização L1 será útil se o objetivo for ter um modelo que seja o mais esparso possível.

        A regularização L1 é feita subtraindo-se o peso de L1 do vetor de peso da expressão de perda que o aprendiz está tentando minimizar. A norma L1 é uma boa aproximação para a norma L0, que é o número de coordenadas diferente de zero.

    - A regularização L2 impede o crescimento excessivo de qualquer coordenada única em magnitude. A regularização L2 é útil se o objetivo é ter um modelo com pequenos pesos gerais.

    Neste módulo, você pode aplicar uma combinação de regularizações L1 e L2. Combinando a regularização L1 e L2, você pode impor uma penalidade na magnitude dos valores de parâmetro. O aprendiz tenta minimizar a penalização, em um compromisso com minimização de perda.

    Para obter uma boa discussão sobre a regularização de L1 e L2, consulte [regularização de L1 e L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. **Tamanho da memória para L-BFGS** : Especifique a quantidade de memória a ser reservada para ajuste e otimização do modelo.

     L-BFGS é um método específico para otimização, com base no algoritmo Broyden – Fletcher – Goldfarb – Shanno (BFGS). O método usa uma quantidade limitada de memória (L) para calcular a direção da próxima etapa.

     Ao alterar esse parâmetro, você pode afetar o número de posições passadas e gradientes que são armazenados para cálculo da próxima etapa.

7. Conecte o conjunto de e-o de treinamento e o modelo não treinado a um dos módulos de treinamento: 

    - Se você definir **criar modo de instrutor** como **um único parâmetro** , use o módulo [treinar modelo](train-model.md) .

    - Se você definir **criar modo de instrutor** como **intervalo de parâmetros** , use o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Se você passar um intervalo de parâmetros para [treinar o modelo](train-model.md), ele usará apenas o primeiro valor na lista intervalo de parâmetros.
    > 
    > - Se você passar um único conjunto de valores de parâmetro para o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) , quando ele esperar um intervalo de configurações para cada parâmetro, ele ignorará os valores e usará os valores padrão para o aprendiz.
    > 
    > - Se você selecionar a opção **intervalo de parâmetros** e inserir um único valor para qualquer parâmetro, esse valor único especificado será usado em toda a varredura, mesmo que outros parâmetros sejam alterados em um intervalo de valores.

8.  Enviar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione o módulo de treinamento e, em seguida, vá para a guia **saídas + logs** no painel direito. Clique no ícone **registrar conjunto de registros**.  Você pode encontrar o modelo salvo como um módulo na árvore de módulo. 

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.