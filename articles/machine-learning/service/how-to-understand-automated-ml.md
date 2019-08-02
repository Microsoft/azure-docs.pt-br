---
title: Entender os resultados de ML automatizados
titleSuffix: Azure Machine Learning service
description: Saiba como exibir e entender os gráficos e as métricas para cada uma das execuções automatizadas do Machine Learning.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: ea85a0906ce231312c491d31a33c331480d23812
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362149"
---
# <a name="understand-automated-machine-learning-results"></a>Entender os resultados automatizados do Machine Learning

Neste artigo, você aprenderá a exibir e a entender os gráficos e as métricas de cada uma das execuções automatizadas do Machine Learning. 

Saiba mais sobre:
+ [Métricas, gráficos e curvas para modelos de classificação](#classification)
+ [Métricas, gráficos e grafos para modelos de regressão](#regression)
+ [Importância da interpretação do modelo e do recurso](#explain-model)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Crie um experimento de Machine Learning automatizado, seja com o SDK ou na portal do Azure.

    * Usar o SDK para criar um modelo de [classificação](how-to-auto-train-remote.md) ou um [modelo](tutorial-auto-train-models.md) de regressão
    * Use o [portal do Azure](how-to-create-portal-experiments.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="view-the-run"></a>Exibir a execução

Depois de executar um experimento de aprendizado de máquina automatizado, um histórico das execuções pode ser encontrado em seu espaço de trabalho do serviço de Machine Learning. 

1. Vá até seu workspace.

1. No painel esquerdo do espaço de trabalho, selecione **experimentos**.

   ![Captura de tela do menu de experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na lista de experimentos, selecione aquele que você deseja explorar.

   ![Lista de experimentos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)

1. Na tabela inferior, selecione o **número de execução**.

   ![Execução do experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)

1. Na tabela iterações, selecione o **número** de iteração para o modelo que você deseja explorar ainda mais.

   ![Modelo do experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)

Você também vê esses mesmos resultados durante uma execução quando usa o `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Resultados da classificação

Três as métricas e os gráficos a seguir estão disponíveis para cada modelo de classificação que você cria usando os recursos de aprendizado de máquina automatizados do Azure Machine Learning

+ [Métricas](#classification-metrics)
+ [Matriz de confusão](#confusion-matrix)
+ [Gráfico de precisão/recall](#precision-recall-chart)
+ [ROC (características operacionais do destinatário)](#roc)
+ [Curva de comparação de precisão](#lift-curve)
+ [Curva de ganhos](#gains-curve)
+ [Gráfico de calibragem](#calibration-plot)

### <a name="classification-metrics"></a>Métricas de classificação

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de classificação.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
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

### <a name="confusion-matrix"></a>Matriz de confusão

Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha exibe as instâncias da classe real, e cada coluna representa as instâncias da classe prevista. A matriz de confusão mostra os rótulos classificados de modo correto e incorreto para um determinado modelo.

Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo compilado. Para cada matriz de confusão, o ML automatizado mostrará em verde e vermelho os rótulos classificados de modo correto e incorreto respectivamente. O tamanho do círculo representa a quantidade de amostras desse compartimento. Além disso, a contagem de frequência de cada rótulo previsto e de cada rótulo real é fornecida em gráficos de barras adjacentes. 

Exemplo 1: Um modelo de classificação com baixa precisão ![Um modelo de classificação com baixa precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Exemplo 2: Um modelo de classificação com alta precisão (recomendado) ![Um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Gráfico de precisão/recall

Com esse gráfico, você pode comparar as curvas de precisão/recall de cada modelo para determinar qual deles tem uma relação aceitável entre a precisão e a recall conforme seu problema comercial específico. Esse gráfico mostra a média macro e micro da precisão/recall, além da precisão/recall associada a todas as classes de um modelo.

O termo “precisão” representa a capacidade de um classificador rotular todas as instâncias corretamente. O termo “recall” representa a capacidade de um classificador localizar todas as instâncias de um rótulo específico. A curva de precisão/recall mostra a relação entre esses dois conceitos. Idealmente, o modelo teria 100% de precisão e exatidão.

Exemplo 1: Um modelo de classificação com baixa precisão e baixo recall ![Um modelo de classificação com baixa precisão e baixo recall](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Exemplo 2: Um modelo de classificação com ~100% de precisão e ~100% de recall (ideal) ![Um modelo de classificação com alta precisão e recall](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

O ROC (característica operacional do receptor) é um gráfico que compara os rótulos classificados de modo correto e incorreto de um modelo específico. A curva ROC pode ser menos informativa ao treinar modelos em conjuntos de dados com alto desvio, pois ela não mostrará os rótulos falso-positivos.

Exemplo 1: Um modelo de classificação com baixa quantidade de rótulos reais e alta quantidade de rótulos falsos ![Modelo de classificação com baixa quantidade de rótulos verdadeiros e alta quantidade de rótulos falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Exemplo 2: Um modelo de classificação com alta quantidade de rótulos verdadeiros e baixa quantidade de rótulos falsos ![Modelo de classificação com alta quantidade de rótulos verdadeiros e baixa quantidade de rótulos falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Curva de comparação de precisão

Você pode comparar a precisão do modelo compilado automaticamente do Azure Machine Learning com a linha de base para exibir o ganho de valor desse determinado modelo.

Gráficos de comparação de precisão são usados para avaliar o desempenho de um modelo de classificação. Eles mostram o quão melhor seu desempenho pode ficar com um modelo em comparação a não ter um modelo. 

Exemplo 1: O modelo tem desempenho pior que um modelo de seleção aleatória ![Um modelo de classificação com desempenho pior do que um modelo de seleção aleatória](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Exemplo 2: O modelo tem desempenho melhor do que um modelo de seleção aleatória ![Um modelo de classificação que tem desempenho melhor](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Curva de ganhos

Um gráfico de ganhos avalia o desempenho de um modelo de classificação em cada porção dos dados. Ele mostra, para cada percentil do conjunto de dados, o quão melhor o desempenho pode ser em comparação a um modelo de seleção aleatória.

Use o gráfico de ganhos cumulativos para ajudar você a escolher o corte de classificação usando uma porcentagem que corresponda a um ganho desejado do modelo. Essas informações fornecem outra maneira de observar os resultados no gráfico de comparação de precisão anexo.

Exemplo 1: Um modelo de classificação com ganho mínimo ![Um modelo de classificação com ganho mínimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Exemplo 2: Um modelo de classificação com ganho significativo ![Um modelo de classificação com ganho significativo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Gráfico de calibragem

Em todos os problemas de classificação, você pode examinar a linha de calibração para cada média micro, macro e cada classe de um determinado modelo de previsão. 

Um gráfico de calibragem é usado para exibir a confiança de um modelo de previsão. Para isso, ele mostra a relação entre a probabilidade prevista e a probabilidade real, em que “probabilidade” representa as chances de uma determinada instância pertencer a algum rótulo. Um modelo bem calibrado se alinha com a linha y = x, estando razoavelmente confiante em suas previsões. Um modelo com confiança excessiva se nivela à linha y=0, na qual a probabilidade prevista está presente, mas não há nenhuma probabilidade real.

Exemplo 1: Um modelo mais bem calibrado ![Um modelo mais bem calibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Exemplo 2: Um modelo com confiança excessiva ![Um modelo com confiança excessiva](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Resultados da regressão

Três as métricas e os gráficos a seguir estão disponíveis para cada modelo de regressão que você cria usando os recursos de aprendizado de máquina automatizados do Azure Machine Learning

+ [Métricas](#reg-metrics)
+ [Previsto vs. Real](#pvt)
+ [Histograma de resíduos](#histo)


### <a name="reg-metrics"></a>Métricas de regressão

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de regressão ou de previsão.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
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

### <a name="pvt"></a>Previsto versus verdadeiro

Previsto vs. “Real” mostra a relação entre um valor previsto e seu valor real de correlação para um problema de regressão. Esse gráfico pode ser usado para medir o desempenho de um modelo que, quanto mais próximos à linha y=x linha estiverem seus valores previstos, melhor será a precisão de um modelo de previsão.

Após cada execução, você pode ver um gráfico comparando os dados previstos e reais de cada modelo de regressão. Para proteger a privacidade dos dados, os valores ficam no mesmo compartimento, sendo que o tamanho de cada compartimento é mostrado como um gráfico de barras na parte inferior da área do gráfico. Você pode comparar o modelo de previsão, com as áreas de tonalidade mais leves, mostrando as margens de erro comparadas ao valor ideal de onde o modelo deveria estar.

Exemplo 1: Um modelo de regressão com a baixa precisão em previsões ![Um modelo de regressão com baixa precisão em previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Exemplo 2: Um modelo de regressão com a alta precisão em previsões ![Um modelo de regressão com alta precisão em previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)



### <a name="histo"></a>Histograma de resíduos

Um resíduo representa um y observado, que é o y previsto. Para mostrar uma margem de erro com baixo desvio, o histograma de resíduos deve ter a forma de uma curva de sino, centralizada em torno do 0. 

Exemplo 1: Um modelo de regressão com desvio em seus erros ![Modelo de regressão de SA com desvio em seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Exemplo 2: Um modelo de regressão com uma distribuição mais uniforme de erros ![Um modelo de regressão com uma distribuição mais uniforme de erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Importância da interpretação do modelo e do recurso

A importância dos recursos permite que você veja quanto valor cada recurso estava na construção de um modelo. Esse cálculo é desativado por padrão, pois pode aumentar significativamente o tempo de execução.   Você pode habilitar a explicação do modelo para todos os modelos ou explicar apenas o modelo de melhor ajuste.

É possível analisar a pontuação de importância do recurso do modelo geral, bem como por classe de um modelo de previsão. Também é possível ver, em cada recurso, como a importância se compara a cada classe e no geral.

![Capacidade de explicar o recurso](./media/how-to-understand-automated-ml/feature-importance.gif)

Para obter mais informações sobre como habilitar recursos de interpretação, consulte [Configurar experimentos de ml automatizados em Python](how-to-configure-auto-train.md#explain-the-model-interpretability).  Para obter um exemplo que explica o melhor modelo, consulte a [melhor explicação do modelo](how-to-auto-train-remote.md#explain).

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre o [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Experimente o bloco de anotações de exemplo [explicação do modelo de Machine Learning automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
