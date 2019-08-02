---
title: Métricas de precisão de treinamento em ML automatizado
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre as métricas de precisão de aprendizado de máquina automatizadas para cada uma de suas execuções.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: dc147fd0252b2b5ec4ce334d6c1c464d9cde8ef5
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297907"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>Avaliar a precisão de treinamento em ML automatizado com métricas

Neste artigo, você aprenderá sobre as diferentes métricas disponíveis para modelos de ml automatizados no Azure Machine Learning. 

Há várias maneiras de exibir as métricas de precisão de treinamento para cada iteração de execução.
* Usar [um widget Jupyter](how-to-track-experiments.md#view-run-details)
* Usar [a `get_metrics()` função](how-to-track-experiments.md#query-run-metrics) em qualquer `Run` objeto
* Exibir [as métricas de experimento no portal do Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="prerequisites"></a>Pré-requisitos
 
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.
 
* Crie um experimento de Machine Learning automatizado, seja com o SDK ou na portal do Azure.
 
    * Usar o SDK para criar um modelo de [classificação](how-to-auto-train-remote.md) ou um [modelo](tutorial-auto-train-models.md) de regressão
    * Use o [portal do Azure](how-to-create-portal-experiments.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="classification-metrics"></a>Métricas de classificação

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de classificação.

|Métrica|DESCRIÇÃO|Cálculo|Parâmetros adicionais
--|--|--|--|
AUC_Macro| AUC é a área embaixo da Curva característica operacional do receptor. Macro é a média aritmética do AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC é a área embaixo da Curva característica operacional do receptor. Micro é calculado globalmente combinando os positivos verdadeiros e falsos positivos de cada classe| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC é a área embaixo da Curva característica operacional do receptor. Ponderada é a média aritmética da pontuação para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy é o percentual de rótulos previstos que coincidem exatamente com os rótulos verdadeiros. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhum|
average_precision_score_macro|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. Macro é a média aritmética da pontuação média de precisão de cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. Micro é calculado globalmente combinando os positivos verdadeiros e falsos positivos em cada corte|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. Ponderada é a média aritmética da pontuação de precisão média para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Precisão equilibrada é a média aritmética do recolhimento de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Pontuação F1 é a média harmônica de precisão e recuperação. Macro é a média aritmética da pontuação F1 cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Pontuação F1 é a média harmônica de precisão e recuperação. Micro é computado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Pontuação F1 é a média harmônica de precisão e recuperação. Média ponderada por frequência de classe ou pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Essa é a função de perda usada na regressão logística (multinomial) e nas extensões dela como redes neurais, definidas como a probabilidade logarítmica negativa dos rótulos verdadeiros dadas as previsões de um classificador probabilístico. Para obter um único exemplo com rótulo verdadeiro yt no {0,1} e a probabilidade estimada yp que yt = 1, a perda logarítmica é -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhum|
norm_macro_recall|O Recolhimento de macro normalizado é o recolhimento de macro normalizado para que o desempenho aleatório tenha uma pontuação de 0 e o desempenho perfeito tenha uma pontuação de 1. Isso é obtido por norm_macro_recall := (recall_score_macro - R)/(1 - R), em que R é o valor esperado de recall_score_macro para previsões aleatórias (i.e., R=0,5 para classificação binária e R=(1/C) para problemas de classificação da classe C)|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro" e (recall_score_macro - R)/(1 - R), em que R é o valor esperado de recall_score_macro para previsões aleatórias (i.e., R=0,5 para classificação binária e R=(1/C) para problemas de classificação da classe C)|
precision_score_macro|A precisão é o percentual de elementos rotulados como uma determinada classe que realmente estão nessa classe. Macro é a média aritmética de precisão para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|A precisão é o percentual de elementos rotulados como uma determinada classe que realmente estão nessa classe. Micro é calculado globalmente contando o total de positivos verdadeiros e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|A precisão é o percentual de elementos rotulados como uma determinada classe que realmente estão nessa classe. Weighted é a média aritmética da precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|O recolhimento é o percentual de elementos que realmente estão em uma determinada classe que são rotulados corretamente. Macro é a média aritmética de recolhimento para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|O recolhimento é o percentual de elementos que realmente estão em uma determinada classe que são rotulados corretamente. Micro é computado globalmente contando o total de positivos verdadeiros e falsos negativos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|O recolhimento é o percentual de elementos que realmente estão em uma determinada classe que são rotulados corretamente. Weighted é a média aritmética de recolhimento para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Precisão ponderada é a precisão em que o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na classe verdadeira desse exemplo|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual à proporção dessa classe para cada elemento no destino|

## <a name="regression-and-forecasting-metrics"></a>Regressão e métricas de previsão

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de regressão ou de previsão.

|Métrica|DESCRIÇÃO|Cálculo|Parâmetros adicionais
--|--|--|--|
explained_variance|A variação explicada é a proporção na qual um modelo matemático responde pela variação de determinado conjunto de dados. É o percentual de redução na variação dos dados originais para a variação dos erros. Quando a média dos erros é 0, ela é igual à variação explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhum|
r2_score|R2 é o coeficiente de determinação ou a redução de percentual em erros quadráticos, comparado a um modelo de linha de base que gera a média. Quando a média dos erros é 0, ela é igual à variação explicada.|[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhum|
spearman_correlation|Correlação de Spearman é uma medida não paramétrica da monotonicidade da relação entre dois conjuntos de dados. Diferentemente da correlação de Pearson, a correlação de Spearman não supõe que os dois conjuntos de dados estão distribuídos normalmente. Como outros coeficientes de correlação, esse varia entre -1 e +1 com 0 implicando nenhuma correlação. As correlações de -1 ou +1 implicam uma relação monotônica exata. Correlações positivas implicam que, à medida que x aumenta, y também. Correlações negativas implicam que, à medida que x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhum|
mean_absolute_error|Erro médio absoluto é o valor esperado do valor absoluto da diferença entre a meta e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhum|
normalized_mean_absolute_error|Erro médio absoluto normalizado é o Erro médio absoluto dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividir pelo intervalo dos dados|
median_absolute_error|O erro absoluto mediano é a mediana de todas as diferenças absolutas entre a meta e a previsão. Essa perda é robusta para exceções.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhum|
normalized_median_absolute_error|Erro absoluto mediano normalizado é o Erro absoluto mediano dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividir pelo intervalo dos dados|
root_mean_squared_error|A raiz do erro quadrático médio é a raiz quadrada da diferença esperada ao quadrado entre a média e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhum|
normalized_root_mean_squared_error|A raiz do erro quadrático médio normalizado é a raiz do erro quadrático médio dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividir pelo intervalo dos dados|
root_mean_squared_log_error|A raiz do erro de log quadrático médio é a raiz quadrada do erro logarítmico quadrático esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhum|
normalized_root_mean_squared_log_error|A raiz do erro de log quadrático médio normalizada é a raiz do erro de log quadrático médio dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividir pelo intervalo dos dados|

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.