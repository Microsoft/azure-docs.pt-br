---
title: 'Classificação: Prever a rotatividade, desejo e venda '
titleSuffix: Azure Machine Learning service
description: Este experimento de exemplo de interface visual mostra a previsão de classificação binária de variação, uma tarefa comum para gerenciamento de relacionamento do cliente (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 7d10d996febd0e31c9085bf5cb82324cce101c80
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606148"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemplo 5: classificação: Prever a rotatividade, desejo e venda 

Saiba como criar um experimento de aprendizado de máquina complexos sem escrever uma única linha de código usando a interface visual.

Nesse experimento treina três, **árvore de decisão aumentada de duas classes** classificadores para prever as tarefas comuns de sistemas CRM (gerenciamento) de relacionamento do cliente: variação, desejo e venda. Os valores de dados e os rótulos são divididos em várias fontes de dados e o mexeu para anonimizar informações do cliente, no entanto, podemos ainda pode usar a interface visual para combinar conjuntos de dados e treinar um modelo usando os valores embaralhados.

Porque estamos tentando responder à pergunta "Qual deles?" Isso é chamado um problema de classificação. No entanto, você pode aplicar as mesmas etapas nesse experimento para lidar com qualquer tipo de problema de aprendizado de máquina, seja em regressão, classificação, clustering e assim por diante.

Aqui está o gráfico completo para esse teste:

![Grafo de experimento](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **abrir** botão para o experimento de amostra 5.

    ![Abra o teste](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Dados

Os dados que usamos para esse teste são da KDD Cup 2009. O conjunto de dados tem 50.000 linhas e colunas de recurso 230. A tarefa é prever a rotatividade, desejo e venda para os clientes que usam esses recursos. Para obter mais informações sobre os dados e a tarefa, consulte o [site KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Resumo do teste

Este experimento de exemplo de interface visual mostra a previsão de classificação binária de variação, desejo e venda, uma tarefa comum para gerenciamento de relacionamento do cliente (CRM).

Primeiro, podemos fazer algum processamento de dados simple.

- O conjunto de dados bruto contém muitos valores ausentes. Podemos usar o **limpar dados ausentes** valores de módulo para substituir o ausente com 0.

    ![Limpar o conjunto de dados](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Os recursos e correspondente de variação, desejo, e rótulos de venda estão em diferentes conjuntos de dados. Podemos usar o **adicionar colunas** módulo para acrescentar as colunas de rótulo para as colunas de recurso. A primeira coluna, **Col1**, é a coluna de rótulo. O restante das colunas, **Var1**, **Var2**e assim por diante, são as colunas de recurso.

    ![Adicionar o conjunto de dados de coluna](./media/ui-sample-classification-predict-churn/added-column1.png)

- Podemos usar o **dividir dados** módulo dividir o conjunto de dados em treinamento e conjuntos de testes.

    Em seguida, usamos o classificador binário da árvore de decisão aumentada com os parâmetros padrão para criar modelos de previsão. Vamos criar um modelo para cada tarefa, ou seja, um modelo para prever a venda, desejo e variação de cada.

## <a name="results"></a>Resultados

Visualizar a saída a **avaliar modelo** módulo para ver o desempenho do modelo no conjunto de teste. Para a tarefa de venda, a curva ROC mostra que o modelo faz melhor do que um modelo aleatório. A área sob a curva (AUC) é 0.857. No limite de 0,5, a precisão é 0,7, o cancelamento é 0.463 e a pontuação F1 é 0.545.

![Avaliar os resultados](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Você pode mover a **limite** controle deslizante e ver as métricas de alterar para a tarefa de classificação binária.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1: regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2: regressão: Comparar algoritmos para previsão de preço de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3: classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4: classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 6 - classificação: Prever os atrasos de voo](ui-sample-classification-predict-flight-delay.md)