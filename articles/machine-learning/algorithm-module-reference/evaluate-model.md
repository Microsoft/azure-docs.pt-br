---
title: 'Avaliar modelo: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Avaliar modelo no Azure Machine Learning para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: 39bdf9cb0c97e19a67b23046c6f06b60daa30147
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584933"
---
# <a name="evaluate-model-module"></a>Módulo Avaliar modelo

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para medir a precisão de um modelo treinado. Você fornece um conjunto de dados que contém pontuações geradas por um modelo, e o módulo **Avaliar modelo** calcula um conjunto de métricas de avaliação padrão do setor.
  
 As métricas retornadas por **Avaliar modelo** dependem do tipo de modelo em avaliação:  
  
-   **Modelos de classificação**    
-   **Modelos de regressão**  
-   **Modelos de clustering**  


> [!TIP]
> Se a avaliação de modelo for novidade para você, recomendamos a série de vídeos do Dr. Stephen Elston, como parte do [curso de aprendizado de máquina](/archive/blogs/machinelearning/new-edx-course-data-science-machine-learning-essentials) da EdX. 


## <a name="how-to-use-evaluate-model"></a>Como usar Avaliar modelo
1. Conecte a saída **Conjunto de dados pontuados** da saída do conjunto de dados [Modelo de pontuação](./score-model.md) ou Resultado de [Atribuir dados aos clusters](./assign-data-to-clusters.md) à porta de entrada esquerda de **Avaliar modelo**. 
    > [!NOTE] 
    > Se usar módulos, como "Selecionar colunas no conjunto de dados", para selecionar parte do conjunto de dados de entrada, verifique se as colunas de rótulo “Real” (usada no treinamento), “Probabilidades pontuadas” e “Rótulos pontuados” existem para calcular métricas, como AUC, Precisão para classificação binária/detecção de anomalias.
    > A coluna de rótulo real e a coluna “Rótulos pontuados” existem para calcular métricas para classificação/regressão multiclasse.
    > A coluna “Atribuições” e as colunas “DistancesToClusterCenter no.X” (X é o índice do centroide, de 0, ..., número de centroides-1) existem para calcular métricas para clustering.

    > [!IMPORTANT]
    > + Para avaliar os resultados, o conjunto de informações de saída deve conter nomes de coluna de Pontuação específicos, que atendem aos requisitos do módulo avaliar modelo.
    > + A `Labels` coluna será considerada como rótulos reais.
    > + Para a tarefa de regressão, o conjunto de pontos a ser avaliado deve ter uma coluna, denominada `Regression Scored Labels` , que representa rótulos pontuados.
    > + Para a tarefa de classificação binária, o conjunto de linhas a ser avaliado deve ter duas colunas, chamadas `Binary Class Scored Labels` , `Binary Class Scored Probabilities` , que representam rótulos pontuados e probabilidades, respectivamente.
    > + Para a tarefa de classificação múltipla, o conjunto de pontos a ser avaliado deve ter uma coluna, denominada `Multi Class Scored Labels` , que representa rótulos pontuados.
    > Se as saídas do módulo upstream não tiverem essas colunas, você precisará modificar de acordo com os requisitos acima.

2. [Opcional] Conecte a saída **Conjunto de dados pontuado** da saída de conjunto de dados [Modelo de pontuação](./score-model.md) ou Resultado de Atribuir dados a clusters para o segundo modelo à porta de entrada **direita** de **Avaliar modelo**. Você pode comparar facilmente os resultados de dois modelos diferentes nos mesmos dados. Os dois algoritmos de entrada devem ter o mesmo tipo de algoritmo. Ou, você pode comparar pontuações de duas execuções diferentes sobre os mesmos dados com parâmetros diferentes.

    > [!NOTE]
    > O tipo de algoritmo consulta “Classificação de duas classes”, “Classificação multiclasse”, “Regressão”, “Clustering” em “Algoritmos de aprendizado de máquina”. 

3. Envie o pipeline para gerar as pontuações de avaliação.

## <a name="results"></a>Resultados

Depois de executar **Avaliar modelo**, selecione o módulo para abrir o painel de navegação **Avaliar modelo** à direita.  Depois, escolha a guia **Saídas + Logs** e, nessa guia, a seção **Saída de dados** tem vários ícones. O ícone **Visualizar** tem ícone de gráfico de barra e é a primeira maneira de ver os resultados.

Para classificação binária, depois de clicar no ícone **Visualizar** , você poderá visualizar a matriz de confusão binária.
Para várias classificações, você pode encontrar o arquivo de plotagem da matriz de confusão na guia **saídas + logs** , como a seguir:
> [!div class="mx-imgBorder"]
> ![Visualização da imagem carregada](media/module/multi-class-confusion-matrix.png)

Se você conectar conjuntos de dados a ambas as entradas de **Avaliar modelo**, os resultados conterão as métricas para o conjunto de dados ou ambos os modelos.
O modelo ou os dados anexados à porta à esquerda são apresentados primeiro no relatório, seguidos pelas métricas do conjunto de dados ou pelo modelo anexado na porta à direita.  

Por exemplo, a imagem a seguir representa uma comparação de resultados de dois modelos de clustering criados com os mesmos dados, mas com parâmetros diferentes.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Como esse é um modelo de clustering, os resultados da avaliação são diferentes da comparação de pontuações de dois modelos de regressão ou de dois modelos de classificação. No entanto, a apresentação geral é a mesma. 

## <a name="metrics"></a>Métricas

Esta seção descreve as métricas retornadas para os tipos específicos de modelos com suporte para uso com **Avaliar modelo**:

+ [modelos de classificação](#metrics-for-classification-models)
+ [modelos de regressão](#metrics-for-regression-models)
+ [modelos de clustering](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Métricas para modelos de classificação


As métricas a seguir são relatadas ao avaliar modelos de classificação binária.
  
-   **Precisão** mede a qualidade de um modelo de classificação como a proporção entre os resultados verdadeiros com o total de casos.  
  
-   A **precisão** é a proporção entre os resultados verdadeiros com os resultados positivos. Precisão = TP/(TP + FP)  
  
-   **Recall** é a fração da quantidade total de instâncias relevantes que foram realmente recuperadas. Recall = TP/(TP + FN)  
  
-   A **Pontuação F1** é calculada como a média ponderada de precisão e RECALL entre 0 e 1, em que o valor de Pontuação F1 ideal é 1.  
  
-   **AUC** mede a área sob a curva plotada com verdadeiros positivos no eixo y e falsos positivos no eixo x. Essa métrica é útil porque ele fornece um único número que permite comparar modelos de tipos diferentes. AUC é classificação-limite-invariável. Ele mede a qualidade das previsões do modelo, independentemente de qual limite de classificação é escolhido.


### <a name="metrics-for-regression-models"></a>Métricas para modelos de regressão
 
As métricas retornadas para modelos de regressão são projetadas para estimar a quantidade de erros.  Um modelo é considerado adequado para os dados se a diferença entre os valores observados e previstos for pequena. No entanto, observar o padrão dos resíduos (a diferença entre qualquer ponto previsto e seu valor real correspondente) pode fornecer muitas informações sobre o desvio potencial de um modelo.  
  
 As métricas a seguir são relatadas para avaliar modelos de regressão.
  
- **Erro absoluto médio (MAE)** mede a distância das previsões dos resultados reais, assim, quanto menor esse valor, melhor.  
  
- **Raiz do quadrado médio (RMSE)** cria um valor único que resume o erro no modelo. Ao elevar a diferença ao quadrado, a métrica desconsidera a diferença entre uma previsão abaixo e uma previsão acima do valor real.  
  
- **Erro relativo absoluto (RAE)** é a diferença relativa absoluta entre os valores esperado e real. Ele é relativo porque a diferença média é dividida pela média aritmética.  
  
- **Erro relativo ao quadrado (RSE)** normaliza similarmente o erro ao quadrado total dos valores previstos ao dividir pelo erro total ao quadrado dos valores reais.  
  

  
- **Coeficiente de determinação**, frequentemente chamado de R <sup>2</sup>, representa o poder de previsão do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (não explica nada) e 1 significa que há um ajuste perfeito. No entanto, deve-se ter cuidado ao interpretar valores de R<sup>2</sup>, pois valores baixos podem ser totalmente normais e valores altos podem ser suspeitos.

###  <a name="metrics-for-clustering-models"></a>Métricas para modelos de clustering

Como os modelos de clustering diferem significativamente dos modelos de classificação e regressão em muitos aspectos, [Avaliar modelo](evaluate-model.md) também retorna um conjunto diferente de estatísticas para modelos de clustering.  
  
 As estatísticas retornadas para um modelo de clustering descrevem quantos pontos de dados foram atribuídos a cada cluster, o valor de separação entre clusters e a densidade dos pontos de dados dentro de cada cluster.  
  
 As estatísticas para o modelo de clustering são calculadas em média em todo o conjunto de dados, com linhas adicionais contendo as estatísticas por cluster.  
  
As métricas a seguir são relatadas para avaliar modelos de clustering.
    
-   As pontuações na coluna **Distância média a outros centros** representam a proximidade, em média, de cada ponto no coluna em relação aos centroides de todos os outros clusters.   

-   As pontuações na coluna **Distância média ao centro de cluster** representam a proximidade de todos os pontos em um cluster ao centroide desse cluster.  
  
-   A coluna **Número de pontos** mostra quantos pontos de dados foram atribuídos a cada cluster, juntamente com o número total geral de pontos de dados em qualquer cluster.  
  
     Se o número de pontos de dados atribuídos a clusters for menor que o número total de pontos de dados disponíveis, isso significará que não foi possível atribuir os pontos de dados a um cluster.  
  
-   As pontuações na coluna, a **distância máxima ao centro de cluster**, representam o máximo de distâncias entre cada ponto e o centróide do cluster desse ponto.  
  
     Se esse número for alto, pode significar que o cluster é amplamente disperso. Você deve examinar essa estatística junto com a **Distância média ao centro de cluster** para determinar o espalhamento do cluster.   

-   A pontuação **Avaliação combinada** na parte inferior de cada seção de resultados lista as pontuações médias dos clusters criados nesse modelo específico.  
  

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.