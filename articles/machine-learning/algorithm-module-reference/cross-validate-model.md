---
title: 'Modelo de validação cruzada: referência de módulo'
titleSuffix: Azure Machine Learning
description: Use o módulo modelo de validação cruzada no designer de Azure Machine Learning para validar as estimativas de parâmetro de classificação ou regressão cruzada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: d4099ecf6e6bcc6654391e54292878393fb22914
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421338"
---
# <a name="cross-validate-model"></a>Modelo de validação cruzada

Este artigo descreve como usar o módulo modelo de validação cruzada no designer de Azure Machine Learning. A *validação cruzada* é uma técnica usada com frequência no aprendizado de máquina para avaliar a variabilidade de um conjunto de dados e a confiabilidade de qualquer modelo treinado por meio desse dado.  

O módulo modelo de validação cruzada usa como entrada um conjunto de dados rotulado, junto com um modelo de classificação ou regressão não treinado. Ele divide o conjunto de valores em alguns subconjuntos (*dobras*), cria um modelo em cada dobra e retorna um conjunto de estatísticas de precisão para cada dobra. Ao comparar as estatísticas de precisão de todas as dobras, você pode interpretar a qualidade do conjunto de dados. Em seguida, você pode entender se o modelo é suscetível a variações nos dados.  

O modelo de validação cruzada também retorna resultados previstos e probabilidades para o conjunto de os, para que você possa avaliar a confiabilidade das previsões.  

### <a name="how-cross-validation-works"></a>Como funciona a validação cruzada

1. A validação cruzada divide aleatoriamente os dados de treinamento em dobras. 

   O algoritmo padroniza para 10 dobras se anteriormente você não particionou do conjunto de dados. Para dividir o conjunto de espaço em um número diferente de dobras, você pode usar o módulo [partição e exemplo](partition-and-sample.md) e indicar quantas dobras deseja usar.  

2.  O módulo separa os dados na dobra 1 para usar para validação. (Às vezes, isso é chamado de *dobra* de controle.) O módulo usa as dobras restantes para treinar um modelo. 

    Por exemplo, se você criar cinco dobras, o módulo gerará cinco modelos durante a validação cruzada. O módulo treina cada modelo usando quatro quintas dos dados. Ele testa cada modelo no restante de um quinto.  

3.  Durante o teste do modelo para cada dobra, o módulo avalia várias estatísticas de precisão. As estatísticas que o módulo usa dependem do tipo de modelo que você está avaliando. Estatísticas diferentes são usadas para avaliar modelos de classificação versus modelos de regressão.  

4.  Quando o processo de criação e avaliação é concluído para todas as dobras, o modelo de validação cruzada gera um conjunto de métricas de desempenho e resultados pontuados para todos os dados. Examine essas métricas para ver se alguma única dobra tem precisão alta ou baixa. 

### <a name="advantages-of-cross-validation"></a>Vantagens da validação cruzada

Uma maneira diferente e comum de avaliar um modelo é dividir os dados em um conjunto de treinamento e teste usando [dividir dados](split-data.md)e validar o modelo nos dados de treinamento. Mas a validação cruzada oferece algumas vantagens:  

-   A validação cruzada usa mais dados de teste.

    A validação cruzada mede o desempenho do modelo com os parâmetros especificados em um espaço de dados maior. Ou seja, a validação cruzada usa todo o conjunto de teste de treinamento para treinamento e avaliação, em vez de uma parte. Por outro lado, se você validar um modelo usando dados gerados de uma divisão aleatória, normalmente você avaliará o modelo em apenas 30% ou menos dos dados disponíveis.  

    No entanto, como a validação cruzada treina e valida o modelo várias vezes em um conjunto de informações maior, é muito mais intensivo computacionalmente. Demora muito mais do que validar em uma divisão aleatória.  

-   A validação cruzada avalia o conjunto de ambos e o modelo.

    A validação cruzada não simplesmente mede a precisão de um modelo. Ele também lhe dá uma ideia de como o representativo do conjunto de dados e de quão confidencial o modelo pode ser para variações nos mesmos.  

## <a name="how-to-use-cross-validate-model"></a>Como usar o modelo de validação cruzada

A validação cruzada pode levar muito tempo para ser executada se o conjunto de seus conjuntos de seus for grande.  Portanto, você pode usar o modelo de validação cruzada na fase inicial de criação e teste do seu modelo. Nessa fase, você pode avaliar a sorte dos parâmetros do modelo (supondo que o tempo de computação seja tolerável). Você pode treinar e avaliar seu modelo usando os parâmetros estabelecidos com os módulos modelo de [treinamento](train-model.md) e [avaliar](evaluate-model.md) modelo.

Nesse cenário, você treina e testa o modelo usando o modelo de validação cruzada.

1. Adicione o módulo modelo de validação cruzada ao seu pipeline. Você pode encontrá-lo no Azure Machine Learning designer, na categoria de **avaliação de Pontuação do modelo &** . 

2. Conecte a saída de qualquer modelo de classificação ou regressão. 

    Por exemplo, se você estiver usando a **árvore de decisão aumentada de duas classes** para classificação, configure o modelo com os parâmetros desejados. Em seguida, arraste um conector da porta de **modelo não treinada** do classificador para a porta correspondente do modelo de validação cruzada. 

    > [!TIP] 
    > Você não precisa treinar o modelo, pois o modelo de validação cruzada treina automaticamente o modelo como parte da avaliação.  
3.  Na porta do **conjunto** de um modelo de validação cruzada, conecte qualquer conjunto de DataSet de treinamento rotulado.  

4.  No painel direito do modelo de validação cruzada, clique em **Editar coluna**. Selecione a única coluna que contém o rótulo de classe ou o valor previsível. 

5. Defina um valor para o parâmetro **semente aleatória** se desejar repetir os resultados da validação cruzada entre execuções sucessivas nos mesmos dados.  

6. Envie o pipeline.

7. Consulte a seção [resultados](#results) para obter uma descrição dos relatórios.

## <a name="results"></a>Resultados

Depois que todas as iterações forem concluídas, o modelo de validação cruzada criará pontuações para todo o conjunto de resultados. Ele também cria métricas de desempenho que você pode usar para avaliar a qualidade do modelo.

### <a name="scored-results"></a>Resultados classificados

A primeira saída do módulo fornece os dados de origem para cada linha, junto com alguns valores previstos e probabilidades relacionadas. 

Para exibir os resultados, no pipeline, clique com o botão direito do mouse no módulo modelo de validação cruzada. Selecione **Visualizar resultados pontuados**.

| Nome da nova coluna      | Descrição                              |
| -------------------- | ---------------------------------------- |
| Rótulos classificados        | Essa coluna é adicionada ao final do conjunto de uma. Ele contém o valor previsto para cada linha. |
| Probabilidades pontuadas | Essa coluna é adicionada ao final do conjunto de uma. Indica a probabilidade estimada do valor em **Rótulos pontuados**. |
| Número de dobra          | Indica o índice de base zero do dobramento ao qual cada linha de dados foi atribuída durante a validação cruzada. |

 ### <a name="evaluation-results"></a>Resultados de avaliação

O segundo relatório é agrupado por dobras. Lembre-se de que durante a execução, o modelo de validação cruzada divide aleatoriamente os dados de treinamento em *n* dobras (por padrão, 10). Em cada iteração do conjunto de um, o modelo de validação cruzada usa uma dobra como um conjunto de uma validação. Ele usa as *n-1* dobras restantes para treinar um modelo. Cada um dos modelos *n* é testado em relação aos dados em todas as outras dobras.

Neste relatório, as dobras são listadas por valor de índice, em ordem crescente.  Para fazer a ordem em qualquer outra coluna, você pode salvar os resultados como um conjunto de um.

Para exibir os resultados, no pipeline, clique com o botão direito do mouse no módulo modelo de validação cruzada. Selecione **Visualizar resultados da avaliação por dobra**.


|Nome da coluna| Descrição|
|----|----|
|Número de dobra| Um identificador para cada dobra. Se você tiver criado cinco dobras, haveria cinco subconjuntos de dados, numerados de 0 a 4.
|Número de exemplos em dobra|O número de linhas atribuídas a cada dobra. Eles devem ser aproximadamente iguais. |


O módulo também inclui as seguintes métricas para cada dobra, dependendo do tipo de modelo que você está avaliando: 

+ **Modelos de classificação**: precisão, recall, F-score, AUC, precisão  

+ **Modelos de regressão**: erro absoluto médio, erro de raiz quadrada média, erro absoluto relativo, erro de quadrado relativo e coeficiente de determinação


## <a name="technical-notes"></a>Observações técnicas  

+ É uma prática recomendada normalizar conjuntos de valores antes de usá-los para validação cruzada. 

+ O modelo de validação cruzada é muito mais intensivo computacionalmente e leva mais tempo para ser concluído do que se você validou o modelo usando um conjunto de informações dividido aleatoriamente. O motivo é que o modelo de validação cruzada treina e valida o modelo várias vezes.

+ Não há necessidade de dividir o conjunto de linhas em conjuntos de treinamento e teste quando você usa a validação cruzada para medir a precisão do modelo. 


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

