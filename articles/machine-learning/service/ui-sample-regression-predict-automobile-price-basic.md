---
title: 'Regressão: Prever o preço'
titleSuffix: Azure Machine Learning service
description: Saiba como criar um modelo de machine learning para prever o preço de um automóvel sem escrever uma única linha de código.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: ddc7ae10581075127f72dd020c59cf28bbfc9ae2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606128"
---
# <a name="sample-1---regression-predict-price"></a>Exemplo 1: regressão: Prever o preço

Saiba como criar um modelo de regressão de aprendizado sem escrever uma única linha de código usando a interface visual.

Experimento treina uma **regressor de floresta de decisão** para prever um carro do preço com base em recursos técnicos, como fabricante, modelo, potência e tamanho. Porque estamos tentando responder à pergunta "Quanto?" Isso é chamado um problema de regressão. No entanto, você pode aplicar as mesmas etapas fundamentais nesse experimento para lidar com qualquer tipo de problema de aprendizado de máquina, seja em regressão, classificação, clustering e assim por diante.

As etapas fundamentais de uma modelo de aprendizado de máquina de treinamento são:

1. Obter os dados
1. Pré-processar os dados
1. Treinar o modelo
1. Avalie o modelo

Aqui está o gráfico final, concluído do teste, trabalharemos no. Forneceremos a lógica para todos os módulos para que possa tomar decisões semelhantes por conta própria.

![Grafo do experimento](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **abrir** botão para o experimento de exemplo 1:

    ![Abra o teste](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Obter os dados

Nesse experimento, podemos usar o **dados de preço de automóvel (brutos)** dataset, que é do repositório de aprendizado de máquina UCI. O conjunto de dados contém 26 colunas que contêm informações sobre automóveis, incluindo marca, modelo, preço, recursos de veículo (como o número de cilindros), MPG e uma pontuação de risco de seguros. O objetivo deste experimento é prever o preço do carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As tarefas de preparação de dados principal incluem a limpeza de dados, integração, transformação, redução e diferenciação ou quantização. Na interface do visual, você pode encontrar módulos para executar essas operações e outros dados de tarefas de pré-processamento a **transformação de dados** grupo no painel esquerdo.

Podemos usar o **selecionar colunas no conjunto de dados** módulo para excluir perdas normalizadas que têm muitos valores ausentes. Em seguida, usamos **limpar dados ausentes** para remover as linhas que têm valores ausentes. Isso ajuda a criar um conjunto de limpo de dados de treinamento.

![Pré-processamento de dados](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Treinar o modelo

Problemas de aprendizado de máquina variam de acordo. Tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, cada um deles pode exigir um algoritmo diferente. Sua escolha de algoritmo geralmente depende dos requisitos de caso de uso. Depois que você escolher um algoritmo, você precisará ajustar seus parâmetros para treinar um modelo mais preciso. Em seguida, você precisa avaliar todos os modelos com base nas métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo deste experimento é prever preços de automóveis, e a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão é provavelmente não linear. Portanto, usamos **regressão da floresta de decisão** para esse teste.

Podemos usar o **dividir dados** módulo dividir aleatoriamente os dados de entrada para que o conjunto de dados de treinamento contém 70% dos dados originais e o conjunto de dados de teste contém 30% dos dados originais.

## <a name="test-evaluate-and-compare"></a>Testar, avaliar e comparar

 Dividimos o conjunto de dados e usar diferentes conjuntos de dados para treinar e testar o modelo para fazer a avaliação do modelo de objetivo mais.

Depois que o modelo é treinado, podemos usar o **modelo de pontuação** e **avaliar modelo** módulos para gerar resultados previstos e avaliar os modelos.

**Modelo de pontuação** gera previsões para o conjunto de dados de teste usando o modelo treinado. Para verificar os resultados, selecione a porta de saída **modelo de pontuação** e, em seguida, selecione **visualizar**.

![Resultados da pontuação](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Em seguida, passamos as pontuações para o **avaliar modelo** módulo para gerar métricas de avaliação. Para verificar os resultados, selecione a porta de saída a **modelo de avaliação** e, em seguida, selecione **visualizar**.

![Avaliar os resultados](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 2: regressão: Comparar algoritmos para previsão de preço de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3: classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4: classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5: classificação: Prever a variação](ui-sample-classification-predict-churn.md)
- [Exemplo 6 - classificação: Prever os atrasos de voo](ui-sample-classification-predict-flight-delay.md)