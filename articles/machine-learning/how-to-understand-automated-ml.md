---
title: Avaliar os resultados do experimento do AutoML
titleSuffix: Azure Machine Learning
description: Saiba como exibir e avaliar os gráficos e as métricas de cada execução de teste automatizado do Machine Learning.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: c95a75ef48aa9e3db070c6c237f913fabbe893fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388202"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Avaliar os resultados do experimento do Machine Learning automatizado

Neste artigo, saiba como avaliar e comparar modelos treinados por seu experimento automatizado do Machine Learning (ML automatizado). No decorrer de um experimento de ML automatizado, muitas execuções são criadas e cada execução cria um modelo. Para cada modelo, ML automatizado gera métricas de avaliação e gráficos que ajudam a medir o desempenho do modelo. 

Por exemplo, o ML automatizado gera os gráficos a seguir com base no tipo de experimento.

| Classificação| Regressão/previsão |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Matriz de confusão](#confusion-matrix)                       | [Histograma de residuais](#residuals)        |
| [Curva de característica operacional do receptor (ROC)](#roc-curve) | [Previsto versus verdadeiro](#predicted-vs-true) |
| [Curva de recall de precisão (PR)](#precision-recall-curve)      |                                          |
| [Curva de comparação de precisão](#lift-curve)                                   |                                          |
| [Curva de ganhos cumulativos](#cumulative-gains-curve)           |                                          |
| [Curva de calibragem](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. (Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://aka.ms/AMLFree) antes de começar)
- Um experimento Azure Machine Learning criado com um:
  - O [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) (nenhum código necessário)
  - O [SDK do Azure Machine Learning Python](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Exibir resultados da execução

Após a conclusão do experimento do ML automatizado, um histórico das execuções pode ser encontrado por meio de:
  - Um navegador com [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md)
  - Um notebook Jupyter usando o [widget Jupyter do RunDetails](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true)

As etapas e o vídeo a seguir mostram como exibir o histórico de execuções e os gráficos e as métricas de avaliação do modelo no estúdio:

1. [Entre no estúdio](https://ml.azure.com/) e navegue até seu espaço de trabalho.
1. No menu à esquerda, selecione **experimentos**.
1. Selecione seu experimento na lista de experimentos.
1. Na tabela na parte inferior da página, selecione uma execução de ML automatizada.
1. Na guia **modelos** , selecione o **nome do algoritmo** para o modelo que você deseja avaliar.
1. Na guia **métricas** , use as caixas de seleção à esquerda para exibir métricas e gráficos.

![Etapas para exibir as métricas no estúdio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Métricas de classificação

O ML automatizado calcula as métricas de desempenho para cada modelo de classificação gerado para o experimento. Essas métricas se baseiam na implementação do scikit learn. 

Muitas métricas de classificação são definidas para classificação binária em duas classes e exigem a média de classes para produzir uma pontuação para classificação de várias classes. O Scikit-Learn fornece vários métodos de média, três dos quais o ML automatizado expõe: **macro**, **micro** e **ponderado**.

- **Macro** – Calcule a métrica para cada classe e calcule a média não ponderada
- **Micro** -calcular a métrica globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos (independentes de classes).
- **Ponderado** – Calcule a métrica para cada classe e calcule a média ponderada com base no número de amostras por classe.

Embora cada método de média tenha seus benefícios, uma consideração comum ao selecionar o método apropriado é desequilíbrio de classe. Se as classes tiverem diferentes números de amostras, talvez seja mais informativo usar uma média de macro em que as classes minoritárias recebem o peso igual para as classes principais. Saiba mais sobre as [métricas binária vs multiclasse no ml automatizado](#binary-vs-multiclass-classification-metrics). 

A tabela a seguir resume as métricas de desempenho do modelo que o ML automatizado calcula para cada modelo de classificação gerado para o experimento. Para obter mais detalhes, consulte a documentação do scikit-Learn vinculada no campo de **cálculo** de cada métrica. 

|Métrica|Descrição|Cálculo|
|--|--|---|
|AUC | AUC é a área sob a [curva de características operacionais do receptor](#roc-curve).<br><br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1]<br> <br>Os nomes de métrica com suporte incluem, <li>`AUC_macro`, a média aritmética do AUC para cada classe.<li> `AUC_micro`, computados combinando os verdadeiros positivos e falsos positivos de cada classe. <li> `AUC_weighted`, a média aritmética da Pontuação de cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.   |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Exatidão é a proporção de previsões que correspondem exatamente aos rótulos de classe verdadeiros. <br> <br>**Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|A precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisões atingidas em cada limite, com o aumento no recolhimento do limite anterior usado como o peso. <br><br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1]<br> <br>Os nomes de métrica com suporte incluem,<li>`average_precision_score_macro`, a média aritmética da Pontuação de precisão média de cada classe.<li> `average_precision_score_micro`, computados combinando os verdadeiros positivos e falsos positivos em cada corte.<li>`average_precision_score_weighted`, a média aritmética da Pontuação de precisão média para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Precisão equilibrada é a média aritmética do recolhimento de cada classe.<br> <br>**Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|Pontuação F1 é a média harmônica de precisão e recuperação. É uma boa medida equilibrada de falsos positivos e falsos negativos. No entanto, não leva em conta os negativos verdadeiros. <br> <br>**Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1]<br> <br>Os nomes de métrica com suporte incluem,<li>  `f1_score_macro`: a média aritmética da Pontuação F1 para cada classe. <li> `f1_score_micro`: calculado por meio da contagem do total de positivos verdadeiros, falsos negativos e falsos positivos. <li> `f1_score_weighted`: média ponderada por frequência de classe de Pontuação F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Essa é a função de perda usada em regressão logística (multinomial) e extensões de ti, como redes neurais, definidas como a probabilidade negativa de log dos rótulos verdadeiros, dadas as previsões de um classificador probabilística. <br><br> **Objetivo:** Mais próximo de 0 o melhor <br> **Intervalo:** [0, inf)|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| A recall de macro normalizado é a chamada de macro-Average e normalizada, para que o desempenho aleatório tenha uma pontuação de 0, e o desempenho perfeito tenha uma pontuação de 1. <br> <br>**Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>em que, `R` é o valor esperado de `recall_score_macro` para previsões aleatórias.<br><br>`R = 0.5`&nbsp;para &nbsp; &nbsp; classificação binária. <br>`R = (1 / C)` para problemas de classificação de classe C.|
matthews_correlation | O coeficiente de correlação Matthews é uma medida equilibrada de precisão, que pode ser usada mesmo que uma classe tenha muito mais amostras do que outra. Um coeficiente de 1 indica previsão perfeita, 0 previsão aleatória e-1 previsão inversa.<br><br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [-1, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precisão|Precisão é a capacidade de um modelo evitar rotular amostras negativas como positivas. <br><br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1]<br> <br>Os nomes de métrica com suporte incluem, <li> `precision_score_macro`, a média aritmética de precisão para cada classe. <li> `precision_score_micro`, calculado globalmente, contando o total de positivos reais e falsos positivos. <li> `precision_score_weighted`, a média aritmética de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
recall| Recall é a capacidade de um modelo detectar todos os exemplos positivos. <br><br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [0, 1]<br> <br>Os nomes de métrica com suporte incluem, <li>`recall_score_macro`: a média aritmética de recall para cada classe. <li> `recall_score_micro`: calculado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos.<li> `recall_score_weighted`: a média aritmética de recall para cada classe, ponderada pelo número de instâncias true em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|A precisão ponderada é a precisão em que cada amostra é ponderada pelo número total de amostras que pertencem à mesma classe. <br><br>**Objetivo:** Mais próximo de 1 o melhor <br>**Intervalo:** [0, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Métricas de classificação binárias versus multiclasse

O ML automatizado não diferencia entre métricas binárias e multiclasse. As mesmas métricas de validação são relatadas se um conjunto de dado tem duas classes ou mais de duas classes. No entanto, algumas métricas são destinadas à classificação multiclasse. Quando aplicado a um DataSet binário, essas métricas não tratarão nenhuma classe como a `true` classe, como você pode esperar. As métricas que são claramente destinadas a multiclasse são sufixadas com `micro` , `macro` ou `weighted` . Os exemplos incluem,,, `average_precision_score` `f1_score` `precision_score` `recall_score` e `AUC` .

Por exemplo, em vez de calcular a RECALL como `tp / (tp + fn)` , a média de recalls de várias classes ( `micro` , `macro` ou `weighted` ) é calculada em ambas as classes de um conjunto de uma classificação binária. Isso é equivalente a calcular a recall para a `true` classe e a `false` classe separadamente e, em seguida, pegar a média dos dois.

## <a name="confusion-matrix"></a>Matriz de confusão

As matrizes de confusão fornecem um Visual de como um modelo de aprendizado de máquina está fazendo erros sistemáticos em suas previsões para modelos de classificação. A palavra "confusão" no nome vem de um modelo "confuso" ou de exemplos de rotulagem. Uma célula na linha `i` e `j` na coluna em uma matriz de confusão contém o número de amostras no conjunto de teste que pertencem à classe `C_i` e foram classificadas pelo modelo como classe `C_j` .

No estúdio, uma célula mais escura indica um número maior de amostras. A seleção da exibição **normalizada** no menu suspenso será normalizada em cada linha da matriz para mostrar a porcentagem da classe `C_i` prevista para ser classe `C_j` . O benefício da exibição **bruta** padrão é que você pode ver se o desequilíbrio na distribuição de classes reais fez com que o modelo classificasse incorretamente exemplos da classe minoritária, um problema comum em conjuntos de dados desequilibrados.

A matriz de confusão de um bom modelo terá a maioria dos exemplos ao longo da diagonal.

### <a name="confusion-matrix-for-a-good-model"></a>Matriz de confusão para um bom modelo 
![Matriz de confusão para um bom modelo ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Matriz de confusão para um modelo inadequado
![Matriz de confusão para um modelo inadequado](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>Curva ROC

A curva de característica operacional do receptor (ROC) plota a relação entre TPR (taxa positiva de verdadeiro) e para (taxa positiva de falsos) à medida que o limite de decisão é alterado. A curva ROC pode ser menos informativa ao treinar modelos em conjuntos de valores com desequilíbrio de classe alta, já que a classe principal pode desafogar contribuições de classes minoritárias.

A área sob a curva (AUC) pode ser interpretada como a proporção de amostras classificadas corretamente. Mais precisamente, o AUC é a probabilidade de o classificador classificar um exemplo positivo escolhido aleatoriamente maior do que um exemplo negativo escolhido aleatoriamente. A forma da curva fornece uma intuição para a relação entre TPR e para como uma função do limite de classificação ou do limite de decisão.

Uma curva que se aproxima do canto superior esquerdo do gráfico está se aproximando de um 100% TPR e 0% para, o melhor modelo possível. Um modelo aleatório produziria uma curva ROC ao longo da `y = x` linha do canto inferior esquerdo para a parte superior direita. Um modelo pior do que o aleatório teria uma curva ROC que fica abaixo da `y = x` linha.
> [!TIP]
> Para experimentos de classificação, cada um dos gráficos de linha produzidos para modelos de ML automatizados pode ser usado para avaliar o modelo por classe ou fazer a média em todas as classes. Você pode alternar entre esses modos de exibição diferentes clicando em rótulos de classe na legenda à direita do gráfico.
### <a name="roc-curve-for-a-good-model"></a>Curva ROC para um bom modelo
![Curva ROC para um bom modelo](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Curva ROC para um modelo inadequado
![Curva ROC para um modelo inadequado](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Curva de recall de precisão

A curva de recall de precisão plota a relação entre precisão e RECALL à medida que o limite de decisão é alterado. Recall é a capacidade de um modelo detectar todos os exemplos positivos e a precisão é a capacidade de um modelo evitar rotular amostras negativas como positivas. Alguns problemas de negócios podem exigir uma recall mais alta e uma precisão mais alta, dependendo da importância relativa de evitar falsos negativos versus falsos positivos.
> [!TIP]
> Para experimentos de classificação, cada um dos gráficos de linha produzidos para modelos de ML automatizados pode ser usado para avaliar o modelo por classe ou fazer a média em todas as classes. Você pode alternar entre esses modos de exibição diferentes clicando em rótulos de classe na legenda à direita do gráfico.
### <a name="precision-recall-curve-for-a-good-model"></a>Curva de recall de precisão para um bom modelo
![Curva de recall de precisão para um bom modelo](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Curva de recall de precisão para um modelo inadequado
![Curva de recall de precisão para um modelo inadequado](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Curva de ganhos cumulativos

A curva ganhos cumulativos plota a porcentagem de amostras positivas classificadas corretamente como uma função da porcentagem de amostras consideradas onde consideramos os exemplos na ordem de probabilidade prevista.

Para calcular o lucro, primeiro classifique todas as amostras da probabilidade mais alta para a mais baixa prevista pelo modelo. Em seguida, siga `x%` as previsões de confiança mais altas. Divida o número de amostras positivas detectadas `x%` pelo número total de amostras positivas para obter o lucro. O lucro cumulativo é o percentual de amostras positivas que detectamos ao considerar algum percentual dos dados que têm mais probabilidade de pertencer à classe positiva.

Um modelo perfeito classificará todos os exemplos positivos acima de todas as amostras negativas, fornecendo uma curva de ganhos cumulativos composta por dois segmentos retos. A primeira é uma linha com inclinação `1 / x` de `(0, 0)` para `(x, 1)` onde `x` é a fração de exemplos que pertencem à classe positiva ( `1 / num_classes` se as classes forem balanceadas). O segundo é uma linha horizontal de `(x, 1)` para `(1, 1)` . No primeiro segmento, todas as amostras positivas são classificadas corretamente e o lucro cumulativo vai para `100%` dentro do primeiro `x%` dos exemplos considerados.

O modelo aleatório de linha de base terá uma curva de ganhos cumulativos após `y = x` onde `x%` os exemplos considerados apenas sobre `x%` o total de exemplos positivos foram detectados. Um modelo perfeito terá uma curva micro média que toca o canto superior esquerdo e uma linha de média de macro que tem uma inclinação `1 / num_classes` até que o lucro cumulativo seja 100% e, em seguida, horizontal até que o percentual de dados seja 100.
> [!TIP]
> Para experimentos de classificação, cada um dos gráficos de linha produzidos para modelos de ML automatizados pode ser usado para avaliar o modelo por classe ou fazer a média em todas as classes. Você pode alternar entre esses modos de exibição diferentes clicando em rótulos de classe na legenda à direita do gráfico.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Curva de ganhos cumulativos para um bom modelo
![Curva de ganhos cumulativos para um bom modelo](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>A curva de ganhos cumulativos para um modelo inadequado
![A curva de ganhos cumulativos para um modelo inadequado](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Curva de comparação de precisão

A curva de comparação de precisão mostra quantas vezes um modelo é melhor comparado a um modelo aleatório. A comparação de precisão é definida como a taxa de lucro cumulativo para o lucro cumulativo de um modelo aleatório.

Esse desempenho relativo leva em conta o fato de que a classificação fica mais difícil à medida que você aumenta o número de classes. (Um modelo aleatório prevê incorretamente uma fração mais alta de exemplos de um conjunto de um DataSet com 10 classes comparadas a um conjunto de uma com duas classes)

A curva de comparação de linha de base é a `y = 1` linha em que o desempenho do modelo é consistente com o de um modelo aleatório. Em geral, a curva de comparação de precisão para um bom modelo será maior no gráfico e além do eixo x, mostrando que, quando o modelo for mais confiante em suas previsões, ele realiza muitas vezes melhor do que a adivinhação aleatória.

> [!TIP]
> Para experimentos de classificação, cada um dos gráficos de linha produzidos para modelos de ML automatizados pode ser usado para avaliar o modelo por classe ou fazer a média em todas as classes. Você pode alternar entre esses modos de exibição diferentes clicando em rótulos de classe na legenda à direita do gráfico.
### <a name="lift-curve-for-a-good-model"></a>Curva de comparação de precisão para um bom modelo
![Curva de comparação de precisão para um bom modelo](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Curva de aumento de precisão para um modelo inadequado
![Curva de aumento de precisão para um modelo inadequado](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Curva de calibragem

A curva de calibragem plota a confiança de um modelo em suas previsões em relação à proporção de amostras positivas em cada nível de confiança. Um modelo bem calibrado classificará de forma correta 100% das previsões às quais ele atribui 100% de confiança, 50% das previsões que atribui 50% de confiança, 20% das previsões que atribui uma confiança de 20% e assim por diante. Um modelo perfeitamente calibrado terá uma curva de calibragem após a `y = x` linha em que o modelo prevê perfeitamente a probabilidade de que as amostras pertençam a cada classe.

Um modelo quase confiante fará com que as probabilidades de previsão sejam próximas de zero e uma, raramente sem certeza sobre a classe de cada amostra e a curva de calibração será semelhante a "S" retroativa. Um modelo sob confiança atribuirá uma probabilidade menor em média à classe que ela prevê e a curva de calibração associada será semelhante a um "S". A curva de calibragem não descreve a capacidade de um modelo de classificar corretamente, mas em vez disso, sua capacidade de atribuir confiança a suas previsões corretamente. Um modelo incorreto ainda pode ter uma boa curva de calibragem se o modelo atribui corretamente a baixa confiança e alta incerteza.

> [!NOTE]
> A curva de calibragem é sensível ao número de amostras, de modo que um pequeno conjunto de validação pode produzir resultados com ruídos que podem ser difíceis de interpretar. Isso não significa, necessariamente, que o modelo não está bem calibrado.

### <a name="calibration-curve-for-a-good-model"></a>Curva de calibragem para um bom modelo
![Curva de calibragem para um bom modelo](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Curva de calibragem para um modelo inadequado
![Curva de calibragem para um modelo inadequado](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Métricas de regressão/previsão

O ML automatizado calcula as mesmas métricas de desempenho para cada modelo gerado, independentemente de ser um experimento de regressão ou de previsão. Essas métricas também passam por normalização para permitir a comparação entre modelos treinados em dados com intervalos diferentes. Para saber mais, confira [normalização de métrica](#metric-normalization).  

A tabela a seguir resume as métricas de desempenho do modelo geradas para os experimentos de regressão e previsão. Como as métricas de classificação, essas métricas também são baseadas nas implementações de scikit. A documentação apropriada do scikit está vinculada de acordo, no campo **cálculo** .

|Métrica|Descrição|Cálculo|
--|--|--|
explained_variance|A variação explicada mede a extensão para a qual um modelo conta para a variação na variável de destino. É o percentual de redução na variação dos dados originais para a variação dos erros. Quando a média dos erros é 0, ela é igual ao coeficiente de determinação (consulte r2_score abaixo). <br> <br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** (-inf, 1]|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Erro de média absoluta é o valor esperado do valor absoluto da diferença entre o destino e a previsão.<br><br> **Objetivo:** Mais próximo de 0 o melhor <br> **Intervalo:** [0, inf) <br><br> Digita <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, o mean_absolute_error dividido pelo intervalo dos dados. | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Erro de percentual absoluto médio (MAPE) é uma medida da diferença média entre um valor previsto e o valor real.<br><br> **Objetivo:** Mais próximo de 0 o melhor <br> **Intervalo:** [0, inf) ||
median_absolute_error|O erro absoluto mediano é a mediana de todas as diferenças absolutas entre a meta e a previsão. Essa perda é robusta para exceções.<br><br> **Objetivo:** Mais próximo de 0 o melhor <br> **Intervalo:** [0, inf)<br><br>Digita <br> `median_absolute_error`<br> `normalized_median_absolute_error`: a median_absolute_error dividida pelo intervalo dos dados. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R ^ 2 é o coeficiente de determinação ou a porcentagem de redução de erros quadrados em comparação com um modelo de linha de base que gera a média. <br> <br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [-1, 1] <br><br> Observação: R ^ 2 geralmente tem o intervalo (-inf, 1], mas valores negativos de clipes de ML automatizados para modelos muito ruins para-1.|[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Erro de quadrado da média raiz (RMSE) é a raiz quadrada da diferença quadrada esperada entre o destino e a previsão. Para um estimador não polarizado, RMSE é igual ao desvio padrão.<br> <br> **Objetivo:** Mais próximo de 0 o melhor <br> **Intervalo:** [0, inf)<br><br>Digita<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: a root_mean_squared_error dividida pelo intervalo dos dados. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Erro de log no quadrado da média raiz é a raiz quadrada do erro logarítmica de quadrado esperado.<br><br>**Objetivo:** Mais próximo de 0 o melhor <br> **Intervalo:** [0, inf) <br> <br>Digita <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: a root_mean_squared_log_error dividida pelo intervalo dos dados.  |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Correlação de Spearman é uma medida não paramétrica da monotonicidade da relação entre dois conjuntos de dados. Diferentemente da correlação de Pearson, a correlação de Spearman não supõe que os dois conjuntos de dados estão distribuídos normalmente. Assim como outros coeficientes de correlação, o Spearman varia entre-1 e 1 com 0, indicando que não há correlação. Correlações de-1 ou 1 implicam uma relação de monotônico exata. <br><br> Spearman é uma métrica de correlação de ordem de classificação que significa que as alterações em valores previstos ou reais não alterarão o resultado de Spearman se não alterarem a ordem de classificação dos valores previstos ou reais.<br> <br> **Objetivo:** Mais próximo de 1 o melhor <br> **Intervalo:** [-1, 1]|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalização de métrica

O ML automatizado normaliza a regressão e as métricas de previsão que permitem a comparação entre modelos treinados em dados com intervalos diferentes. Um modelo treinado em um dado com um intervalo maior tem um erro maior do que o mesmo modelo treinado em dados com um intervalo menor, a menos que esse erro seja normalizado.

Embora não haja um método padrão de normalização de métricas de erro, o ML automatizado usa a abordagem comum de dividir o erro pelo intervalo dos dados: `normalized_error = error / (y_max - y_min)`

Ao avaliar um modelo de previsão em dados de série temporal, o ML automatizado executa etapas adicionais para garantir que a normalização ocorra por ID de série temporal (granulação), porque cada série temporal provavelmente tem uma distribuição diferente dos valores de destino.
## <a name="residuals"></a>Resíduos

O gráfico de resíduos é um histograma dos erros de previsão (resíduos) gerados para experimentos de regressão e previsão. Os resíduos são calculados como `y_predicted - y_true` para todas as amostras e, em seguida, exibidos como um histograma para mostrar a tendência do modelo.

Neste exemplo, observe que ambos os modelos são ligeiramente tendenciosas para prever menor do que o valor real. Isso não é incomum para um conjunto de dados com uma distribuição distorcida de destinos reais, mas indica pior desempenho de modelo. Um bom modelo terá uma distribuição residual que atinge picos de zero com alguns resíduos nos extremos. Um modelo pior terá uma distribuição residual espalhada com menos amostras em volta de zero.

### <a name="residuals-chart-for-a-good-model"></a>Gráfico de resíduos para um bom modelo
![Gráfico de resíduos para um bom modelo](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Gráfico de resíduos para um modelo inadequado
![Gráfico de resíduos para um modelo inadequado](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Previsto versus verdadeiro

Para a regressão e o teste de previsão, o gráfico previsto versus verdadeiro plota a relação entre o recurso de destino (valores verdadeiros/reais) e as previsões do modelo. Os valores verdadeiros são compartimentalizados ao longo do eixo x e para cada compartimento o valor estimado médio é plotado com barras de erro. Isso permite que você veja se um modelo é polarizado para prever determinados valores. A linha exibe a previsão média e a área sombreada indica a variação de previsões em torno dessa média.

Geralmente, o valor verdadeiro mais comum terá as previsões mais precisas com a menor variação. A distância da linha de tendência da linha ideal `y = x` em que há poucos valores verdadeiros é uma boa medida do desempenho do modelo em exceções. Você pode usar o histograma na parte inferior do gráfico para o motivo da distribuição de dados real. Incluir mais exemplos de dados em que a distribuição é esparsa pode melhorar o desempenho do modelo em dados não vistos.

Neste exemplo, observe que o melhor modelo tem uma linha prevista versus verdadeira que está mais próxima da `y = x` linha ideal.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Gráfico previsto versus verdadeiro para um bom modelo
![Gráfico previsto versus verdadeiro para um bom modelo](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Gráfico previsto versus verdadeiro para um modelo inadequado
![Gráfico previsto versus verdadeiro para um modelo inadequado](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Explicações de modelo e importância de recursos

Embora os gráficos e as métricas de avaliação do modelo sejam bons para medir a qualidade geral de um modelo, inspecionando quais recursos de um modelo usado para fazer suas previsões é essencial ao praticar a AI responsável. É por isso que o ML automatizado fornece um painel de interpretação de modelo para medir e relatar as contribuições relativas dos recursos do conjunto de relatórios.

Para exibir o painel de interpretação no estúdio:
1. [Entre no estúdio](https://ml.azure.com/) e navegue até seu espaço de trabalho
2. No menu à esquerda, selecione **experimentos**
3. Selecione seu experimento na lista de experimentos
4. Na tabela na parte inferior da página, selecione uma execução do AutoML
5. Na guia **modelos** , selecione o **nome do algoritmo** para o modelo que você deseja explicar
6. Na guia **explicações** , você pode ver que uma explicação já foi criada se o modelo fosse o melhor
7. Para criar uma nova explicação, selecione **explicar modelo** e selecione a computação remota com a qual computar explicações

[Saiba mais sobre explicações de modelo em ml automatizado](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> O modelo ForecastTCN não é suportado atualmente por explicações de ML automatizadas e outros modelos de previsão podem ter acesso limitado às ferramentas de interpretação.

## <a name="next-steps"></a>Próximas etapas
* Experimente o [modelo de aprendizado de máquina automatizado explicação dos notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* Para perguntas específicas de ML automatizadas, entre em contato com askautomatedml@microsoft.com .
