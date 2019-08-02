---
title: 'Classificação: Prever risco de crédito'
titleSuffix: Azure Machine Learning service
description: Saiba como criar um classificador de aprendizado sem escrever uma única linha de código usando a interface visual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 6ec91ca83d7aa1bc5e6c290d35b573a60cc0ed19
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605769"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exemplo 3: classificação: Prever risco de crédito

Saiba como criar um classificador de aprendizado sem escrever uma única linha de código usando a interface visual. Este exemplo treina uma **árvore de decisão aumentada de duas classes** para prever o crédito risco (alto ou baixo) com base nas informações de aplicativo de crédito, como o histórico de crédito, a idade e o número de cartões de crédito.

Porque estamos tentando responder à pergunta "Qual deles?" Isso é chamado um problema de classificação. No entanto, você pode aplicar o mesmo processo fundamental para lidar com qualquer tipo de problema de aprendizado de máquina, seja em regressão, classificação, clustering e assim por diante.

Aqui está o gráfico completo para esse teste:

![Grafo do experimento](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **abrir** botão para o experimento de exemplo 3:

    ![Abra o teste](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Exemplo relacionado

[Exemplo 4: classificação: (Custo confidenciais) de previsão de risco de crédito](ui-sample-classification-predict-credit-risk-cost-sensitive.md) fornece uma experiência avançada que resolva o problema mesmo como esse teste. Ele mostra como realizar _custo confidencial_ classificação usando uma **Executar Script Python** módulo e comparar o desempenho dos dois algoritmos de classificação binária. Consultá-lo se você quiser saber mais sobre como criar experimentos de classificação.

## <a name="data"></a>Dados

Usamos o conjunto de dados de cartão de crédito alemão do repositório UC Irvine.
O conjunto de dados contém 1.000 amostras com 1 rótulo e 20 recursos. Cada exemplo representa uma pessoa. Os recursos incluem recursos numéricos e categóricos. Consulte a [site UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) do significado dos recursos categóricos. A última coluna é o rótulo, que denota o risco de crédito e tem apenas dois valores possíveis: risco de crédito alto = 2 e o risco de crédito baixo = 1.

## <a name="experiment-summary"></a>Resumo do teste

Vamos seguir estas etapas para criar o experimento:

1. Arraste o módulo de conjunto de dados de dados de UCI de cartão de crédito alemão na tela do experimento.
1. Adicionar um **editar metadados** módulo, portanto, podemos adicionar nomes significativos para cada coluna.
1. Adicionar um **dividir dados** módulo para criar conjuntos de treinamento e teste. Defina a fração de linhas no primeiro conjunto de dados de saída em 0,7. Essa configuração especifica que 70% dos dados será a saída para a porta à esquerda do módulo e o restante para a porta à direita. Usamos o conjunto de dados à esquerda para treinamento e uma adequado para teste.
1. Adicionar um **árvore de decisão aumentada duas classes** módulo para inicializar um classificador de árvore de decisão aumentada.
1. Adicionar um **modelo de treinamento** módulo. Conectar-se a classificação da etapa anterior para a porta de entrada esquerda do **modelo de treinamento**. Adicionar conjunto de treinamento (porta de saída de esquerda a **dividir dados**) para a porta de entrada direita o **treinar modelo**. O **treinar modelo** será treinar o classificador.
1. Adicionar um **modelo de pontuação** módulo e conecte-se a **treinar modelo** módulo a ele. Em seguida, adicione o conjunto de teste (a porta de direita o **dividir dados**) para o **modelo de pontuação**. O **modelo de pontuação** fará as previsões. Você pode selecionar sua porta de saída para ver as previsões e as probabilidades de classe positiva.
1. Adicionar um **avaliar modelo** módulo e conecte-se o conjunto de dados à sua porta de entrada à esquerda. Para ver os resultados da avaliação, selecione a porta de saída a **modelo de avaliação** módulo e selecione **visualizar**.

Aqui está o grafo de experimento completa:

![Grafo do experimento](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Resultados

![Avaliar os resultados](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Os resultados da avaliação, você pode ver que o AUC do modelo é 0.776. No limite de 0,5, a precisão é 0.621, o cancelamento é 0,456 e a pontuação F1 é 0.526.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1: regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2: regressão: Comparar algoritmos para previsão de preço de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 4: classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5: classificação: Prever a variação](ui-sample-classification-predict-churn.md)
- [Exemplo 6 - classificação: Prever os atrasos de voo](ui-sample-classification-predict-flight-delay.md)