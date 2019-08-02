---
title: 'Classificação: Prever o risco de crédito (custo confidencial)'
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como criar uma experimento usando a interface visual de aprendizado de máquina complexa. Você aprenderá a implementar scripts personalizados do Python e comparar vários modelos para escolher a melhor opção.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: efed981b500ff14a66c2355a1d14bd762000622f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606157"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Exemplo 4: classificação: Prever o risco de crédito (custo confidencial)

Este artigo mostra como criar uma experimento usando a interface visual de aprendizado de máquina complexa. Você aprenderá a implementar uma lógica personalizada usando scripts Python e comparar vários modelos para escolher a melhor opção.

Este exemplo treina um classificador para prever o risco de crédito usando a crédito informações do aplicativo, como o histórico de crédito, a idade e o número de cartões de crédito. No entanto, você pode aplicar os conceitos neste artigo para lidar com seu próprios problemas de aprendizado de máquina.

Se você estiver apenas começando com o machine learning, você pode dar uma olhada a [amostra de classificação básica](ui-sample-classification-predict-credit-risk-basic.md) primeiro.

Aqui está o gráfico completo para esse teste:

[![Grafo do experimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **abrir** botão para o experimento de exemplo 4:

    ![Abra o teste](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Dados

Usamos o conjunto de dados de cartão de crédito alemão do repositório UC Irvine. Esse conjunto de dados contém 1.000 amostras com 1 rótulo e 20 recursos. Cada exemplo representa uma pessoa. Os recursos de 20 incluem recursos numéricos e categóricos. Consulte a [site UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) para obter mais informações sobre o conjunto de dados. A última coluna é o rótulo, que denota o risco de crédito e tem apenas dois valores possíveis: risco de crédito alto = 2 e o risco de crédito baixo = 1.

## <a name="experiment-summary"></a>Resumo do teste

Nesse experimento, podemos comparar duas abordagens diferentes para gerar modelos para resolver esse problema:

- Treinamento com o conjunto de dados original.
- Treinamento com um conjunto de dados replicado.

Com as duas abordagens, podemos avaliar os modelos usando o conjunto de dados de teste com a replicação para garantir que os resultados estão alinhados com a função de custo. Podemos testar dois classificadores com as duas abordagens: **Máquina de vetor de suporte de duas classes** e **árvore de decisão aumentada de duas classes**.

O custo de classificar incorretamente um exemplo de risco baixo como alto é 1, e o custo de classificar incorretamente um exemplo de alto risco como baixo é 5. Usamos uma **Executar Script Python** módulo para levar em conta essa classificação incorreta de custo.

Aqui está o grafo do experimento:

[![Grafo do experimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Processamento de dados

Começamos usando o **Editor de metadados** módulo para adicionar nomes de coluna para substituir os nomes de coluna padrão com nomes mais significativos, obtido a partir da descrição do conjunto de dados no site UCI. Nós fornecemos os novos nomes de coluna como valores separados por vírgula na **nova coluna** campo de nome da **Editor de metadados**.

Em seguida, vamos gerar o treinamento e usados para desenvolver o modelo de previsão de risco de conjuntos de testes. Podemos dividir o conjunto de dados original em conjuntos de treinamento e teste do mesmo tamanho usando o **dividir dados** módulo. Para criar conjuntos de tamanho igual, definimos a **fração de linhas no primeiro conjunto de dados de saída** opção como 0,5.

### <a name="generate-the-new-dataset"></a>Gerar um novo conjunto de dados

Como o custo de subestimar o risco é alto, definimos o custo da classificação incorreta, como este:

- Para casos de alto risco classificadas erroneamente como baixo risco: 5
- Para casos de baixo risco classificadas erroneamente como de alto risco: 1

Para refletir essa função de custo, podemos gerar um novo conjunto de dados. No novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, mas o número de exemplos de baixo risco não muda. Podemos dividir os dados em conjuntos de dados de treinamento e teste antes da replicação para impedir que a mesma linha nos dois conjuntos.

Para replicar os dados de alto risco, colocamos esse código Python em um **Executar Script Python** módulo:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

O **Executar Script Python** módulo replica os conjuntos de dados de treinamento e de teste.

### <a name="feature-engineering"></a>Engenharia de recursos

O **máquina de vetor de suporte de duas classes** algoritmo requer dados normalizados. Para que possamos usar o **normalizar dados** módulo normalizar os intervalos de todos os recursos numéricos com um `tanh` transformação. Um `tanh` transformação converte todos os recursos numéricos em valores em um intervalo de 0 e 1, preservando a distribuição global de valores.

O **máquina de vetor de suporte de duas classes** módulo lida com os recursos de cadeia de caracteres, convertendo-os para recursos categóricos e, em seguida, para binários recursos com um valor de 0 ou 1. Portanto, não precisamos normalizar esses recursos.

## <a name="models"></a>Modelos

Como podemos aplicar dois classificadores, **máquina de vetor de suporte de duas classes** (SVM) e **árvore de decisão aumentada duas classes**e também usar dois conjuntos de dados, podemos gerar um total de quatro modelos:

- SVM treinado com os dados originais.
- SVM treinado com dados replicados.
- Árvore de decisão aumentada treinado com os dados originais.
- Árvore de decisão aumentada treinados com dados replicados.

Usamos o fluxo de trabalho experimental padrão para criar, treinar e testar os modelos:

1. Inicializar os algoritmos de aprendizado, usando **máquina de vetor de suporte de duas classes** e **árvore de decisão aumentada duas classes**.
1. Use **modelo de treinamento** para aplicar o algoritmo aos dados e criar o modelo real.
1. Use **modelo de pontuação** para gerar pontuações usando os exemplos de teste.

O diagrama a seguir mostra uma parte desse experimento, na qual os conjuntos de treinamento original e replicados são usados para treinar os dois modelos diferentes de SVM. **Treinar modelo** está conectado ao conjunto de treinamento, e **modelo de pontuação** está conectado ao conjunto de teste.

![Grafo de experimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

No estágio de avaliação do teste, calculamos a precisão de cada um dos quatro modelos. Esse teste, usamos **avaliar modelo** para comparar os exemplos que têm a mesma classificação incorreta de custo.

O **avaliar modelo** módulo pode calcular as métricas de desempenho para até dois modelos pontuados. Para que possamos usar uma instância do **modelo de avaliação** para avaliar os dois modelos SVM e outra instância do **avaliar modelo** para avaliar os dois modelos de árvore de decisão aumentada.

Observe que o conjunto de dados replicados de teste é usado como entrada para **modelo de pontuação**. Em outras palavras, as pontuações de precisão final incluem o custo para obter os rótulos errado.

## <a name="combine-multiple-results"></a>Combinar vários resultados

O **avaliar modelo** módulo gera uma tabela com uma única linha que contém várias métricas. Para criar um único conjunto de resultados de exatidão, primeiro usamos **adicionar linhas** para combinar os resultados em uma única tabela. Em seguida, usamos o seguinte script do Python na **Executar Script Python** módulo para adicionar o nome do modelo e a abordagem de treinamento para cada linha na tabela de resultados:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Resultados

Para exibir os resultados do teste, você pode clique com botão direito a saída de visualizar do último **selecionar colunas no conjunto de dados** módulo.

![Visualizar a saída](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

A primeira coluna lista a algoritmo usado para gerar o modelo de aprendizado de máquina.
A segunda coluna indica o tipo de conjunto de treinamento.
A terceira coluna contém o valor de precisão sensível a custo.

Com base nesses resultados, você pode ver que a maior precisão é fornecida pelo modelo que foi criado com **máquina de vetor de suporte de duas classes** e treinado no conjunto de dados replicados de treinamento.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1: regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2: regressão: Comparar algoritmos para previsão de preço de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3: classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 5: classificação: Prever a variação](ui-sample-classification-predict-churn.md)
- [Exemplo 6 - classificação: Prever os atrasos de voo](ui-sample-classification-predict-flight-delay.md)