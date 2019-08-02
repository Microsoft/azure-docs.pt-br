---
title: 'Regressão: Prever o preço e comparar algoritmos'
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como criar um experimento de aprendizado de máquina complexos sem escrever uma única linha de código usando a interface visual. Saiba como treinar e comparar vários modelos de regressão para prever o preço do carro com base nos recursos técnicos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: aa0a1fc2acdc9687030040c23cdb1781e9529169
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605686"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exemplo 2: regressão: Prever o preço e comparar algoritmos

Saiba como criar um experimento de aprendizado de máquina complexos sem escrever uma única linha de código usando a interface visual. Este exemplo treina e compara vários modelos de regressão para prever o preço do carro com base em seus recursos técnicos. Forneceremos a lógica para as escolhas feitas nesse experimento, portanto, você pode lidar com seu próprios problemas de aprendizado de máquina.

Se você estiver apenas começando com o machine learning, você pode dar uma olhada a [versão básica](ui-sample-regression-predict-automobile-price-basic.md) desse experimento para ver uma regressão básica a experimentar.

Aqui está o gráfico completo para esse teste:

[![Grafo do experimento](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **abrir** botão para o experimento de exemplo 2:

    ![Abra o teste](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Resumo do teste

Usamos estas etapas para criar o experimento:

1. Obter os dados.
1. Pré-processe os dados.
1. Treine o modelo.
1. Testar, avaliar e comparar os modelos.

## <a name="get-the-data"></a>Obter os dados

Nesse experimento, podemos usar o **dados de preço de automóvel (brutos)** dataset, que é do repositório de aprendizado de máquina UCI. Esse conjunto de dados contém 26 colunas que contêm informações sobre automóveis, incluindo marca, modelo, preço, recursos de veículo (como o número de cilindros), MPG e uma pontuação de risco de seguros. O objetivo deste experimento é prever o preço de um carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As tarefas de preparação de dados principal incluem a limpeza de dados, integração, transformação, redução e diferenciação ou quantização. Na interface visual, você pode encontrar módulos para executar essas operações e outros dados de tarefas de pré-processamento a **transformação de dados** grupo no painel esquerdo.

Nesse experimento, podemos usar o **selecionar colunas no conjunto de dados** módulo para excluir perdas normalizadas que têm muitos valores ausentes. Em seguida, usamos **limpar dados ausentes** para remover as linhas que têm valores ausentes. Isso ajuda a criar um conjunto de limpo de dados de treinamento.

![Pré-processamento de dados](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Treinar o modelo

Problemas de aprendizado de máquina variam de acordo. Tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, cada um deles pode exigir um algoritmo diferente. Sua escolha de algoritmo geralmente depende dos requisitos de caso de uso. Depois que você escolher um algoritmo, você precisará ajustar seus parâmetros para treinar um modelo mais preciso. Em seguida, você precisa avaliar todos os modelos com base nas métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo deste experimento é prever preços de automóveis, e a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão é provavelmente não linear.

Para comparar o desempenho dos algoritmos diferentes, usamos dois algoritmos de não-lineares **regressão da árvore de decisão impulsionada** e **regressão de floresta de decisão**, para criar modelos. Ambos os algoritmos têm parâmetros que podem ser alteradas, mas podemos usar os valores padrão para esse teste.

Podemos usar o **dividir dados** módulo dividir aleatoriamente os dados de entrada para que o conjunto de dados de treinamento contém 70% dos dados originais e o conjunto de dados de teste contém 30% dos dados originais.

## <a name="test-evaluate-and-compare-the-models"></a>Testar, avaliar e comparar os modelos

Usamos dois diferentes conjuntos de dados escolhidos aleatoriamente para treinar e testar o modelo, conforme descrito na seção anterior. Dividimos o conjunto de dados e usar diferentes conjuntos de dados para treinar e testar o modelo para fazer a avaliação do modelo de objetivo mais.

Depois que o modelo é treinado, podemos usar o **modelo de pontuação** e **avaliar modelo** módulos para gerar resultados previstos e avaliar os modelos. **Modelo de pontuação** gera previsões para o conjunto de dados de teste usando o modelo treinado. Em seguida, passamos as pontuações para **avaliar modelo** para gerar métricas de avaliação.

Nesse experimento, usamos duas instâncias de **avaliar modelo** para comparar dois pares de modelos.

Primeiro, vamos comparar dois algoritmos no conjunto de dados de treinamento.
Em segundo lugar, podemos comparar dois algoritmos no conjunto de dados de teste.
Estes são os resultados:

![Compare os resultados](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Esses resultados mostram que o modelo criado com **regressão da árvore de decisão impulsionada** tem uma raiz inferior a erro quadrático que o modelo criado no médio **regressão de floresta de decisão**.

Ambos os algoritmos tem um erro mais baixo no conjunto de dados de treinamento que no conjunto de dados de teste nunca visto.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1: regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 3: classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4: classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5: classificação: Prever a variação](ui-sample-classification-predict-churn.md)
- [Exemplo 6 - classificação: Prever os atrasos de voo](ui-sample-classification-predict-flight-delay.md)
