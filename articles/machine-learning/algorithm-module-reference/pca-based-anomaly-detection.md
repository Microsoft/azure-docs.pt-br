---
title: 'Detecção de anomalias PCA-Based: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de detecção de anomalias PCA-Based para criar um modelo de detecção de anomalias com base na análise de componente principal (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898452"
---
# <a name="pca-based-anomaly-detection-module"></a>Módulo de detecção de anomalias PCA-Based

Este artigo descreve como usar o módulo de detecção de anomalias PCA-Based no designer de Azure Machine Learning, para criar um modelo de detecção de anomalias com base na análise de componente principal (PCA).

Esse módulo ajuda você a criar um modelo em cenários em que é fácil obter dados de treinamento de uma classe, como transações válidas, mas difícil obter amostras suficientes das anomalias direcionadas. 

Por exemplo, para detectar transações fraudulentas, geralmente você não tem exemplos de fraudes suficientes para treinar. Mas você pode ter muitos exemplos de transações boas. O módulo de detecção de anomalias PCA-Based resolve o problema analisando os recursos disponíveis para determinar o que constitui uma classe "normal". Em seguida, o módulo aplica métricas de distância para identificar casos que representam anomalias. Essa abordagem permite treinar um modelo usando dados desequilibrados existentes.

## <a name="more-about-principal-component-analysis"></a>Mais sobre a análise de componente principal

O PCA é uma técnica estabelecida no aprendizado de máquina. Ele é frequentemente usado na análise de dados exploratório porque revela a estrutura interna dos dados e explica a variância nos dados.

O PCA trabalha analisando dados que contêm muitas variáveis. Ele procura correlações entre as variáveis e determina a combinação de valores que melhor detecta as diferenças nos resultados. Esses valores de recurso combinados são usados para criar um espaço de recurso mais compacto chamado de *componentes principais*.

Para detecção de anomalias, cada nova entrada é analisada. O algoritmo de detecção de anomalias computa sua projeção no eigenvectors, junto com um erro de reconstrução normalizado. O erro normalizado é usado como a pontuação de anomalias. Quanto maior o erro, mais anormal é a instância.

Para obter mais informações sobre como o PCA funciona e sobre a implementação para detecção de anomalias, consulte estes documentos:

- [Um algoritmo aleatório para análise de componente principal](https://arxiv.org/abs/0809.2274), por Rokhlin, Szlan e Tygert

- [Localizando a estrutura com aleatoriedade: algoritmos probabilística para construir decomposiçãos de matriz aproximadas](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (download de PDF), por Halko, Martinsson e Tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>Como configurar a detecção de anomalias PCA-Based

1. Adicione o módulo **detecção de anomalias com base em PCA** ao seu pipeline no designer. Você pode encontrar esse módulo na categoria de **detecção de anomalias** .

2. No painel direito do módulo, selecione a opção **modo de treinamento** . Indique se você deseja treinar o modelo usando um conjunto específico de parâmetros ou use uma limpeza de parâmetro para encontrar os melhores parâmetros.

    Se você souber como deseja configurar o modelo, selecione a opção de **parâmetro único** e forneça um conjunto específico de valores como argumentos.

3. Para obter o **número de componentes a serem usados no PCA**, especifique o número de recursos de saída ou componentes que você deseja.

    A decisão de quantos componentes incluir é uma parte importante do design do experimento que usa o PCA. As diretrizes gerais são que você não deve incluir o mesmo número de componentes do PCA que há variáveis. Em vez disso, você deve começar com um número menor de componentes e aumentá-los até que algum critério seja atendido.

    Os melhores resultados são obtidos quando o número de componentes de saída é *menor que* o número de colunas de recurso disponíveis no conjunto de recursos.

4. Especifique a quantidade de sobreamostragem a ser executada durante o treinamento aleatório do PCA. Em problemas de detecção de anomalias, os dados desequilibrados dificultam a aplicação das técnicas padrão do PCA. Ao especificar alguma quantidade de sobreamostragem, você pode aumentar o número de instâncias de destino.

    Se você especificar **1**, nenhuma sobreamostração será executada. Se você especificar qualquer valor maior que **1**, serão geradas amostras adicionais para usar no treinamento do modelo.

    Há duas opções, dependendo se você estiver usando uma varredura de parâmetro ou não:

    - **Parâmetro de sobreamostragem para o PCA aleatório**: digite um único número inteiro que representa a proporção de sobreamostragem da classe minoritária sobre a classe normal. (Essa opção está disponível quando você está usando o método de treinamento de **parâmetro único** .)

    > [!NOTE]
    > Não é possível exibir o conjunto de dados com amostra. Para obter mais informações sobre como a Superamostragem é usada com o PCA, consulte [notas técnicas](#technical-notes).

5. Selecione a opção **habilitar o recurso de entrada média de normalização** para normalizar todos os recursos de entrada para uma média de zero. A normalização ou o dimensionamento para zero é geralmente recomendado para o PCA, pois a meta do PCA é maximizar a variação entre variáveis.

    Essa opção é habilitada por padrão. Desmarque-a se os valores já tiverem sido normalizados por meio de um método ou uma escala diferente.

6. Conecte um conjunto de uma de treinamento marcado e um dos módulos de treinamento.

   Se você definir a opção **criar modo de instrutor** como **parâmetro único**, use o módulo [treinar modelo de detecção de anomalias](train-anomaly-detection-model.md) .

7. Enviar o pipeline.

## <a name="results"></a>Resultados

Quando o treinamento for concluído, você poderá salvar o modelo treinado. Ou você pode conectá-lo ao módulo [modelo de Pontuação](score-model.md) para prever pontuações de anomalias.

Para avaliar os resultados de um modelo de detecção de anomalias:

1. Verifique se uma coluna de pontuação está disponível em ambos os conjuntos de os.

    Se você tentar avaliar um modelo de detecção de anomalias e obter o erro "não há nenhuma coluna de pontuação no conjunto de pontos de Pontuação para comparar", você está usando um conjunto de cálculo de avaliação típico que contém uma coluna de rótulo, mas sem pontuações de probabilidade. Escolha um conjunto de um que corresponda à saída de esquema para modelos de detecção de anomalias, que inclui **Rótulos pontuados** e colunas de **probabilidades pontuadas** .

2. Verifique se as colunas de rótulo estão marcadas.

    Às vezes, os metadados associados à coluna de rótulo são removidos no grafo de pipeline. Se isso acontecer, quando você usar o módulo [modelo de avaliação](evaluate-model.md) para comparar os resultados de dois modelos de detecção de anomalias, você poderá obter o erro "não há nenhuma coluna de rótulo no conjunto de linha de DataSet". Ou você pode receber o erro "não há nenhuma coluna de rótulo no conjunto de marcação para comparar".

    Você pode evitar esses erros adicionando o módulo [Editar metadados](edit-metadata.md) antes do módulo [modelo de avaliação](evaluate-model.md) . Use o seletor de coluna para escolher a coluna de classe e, na lista **campos** , selecione **rótulo**.

3. Use o módulo [Executar script Python](execute-python-script.md) para ajustar as categorias de coluna de rótulo como **1 (positivo, normal)** e **0 (negativo, anormal)**.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Observações técnicas

Esse algoritmo usa o PCA para aproximar o subespaço que contém a classe normal. O subespaço é estendido por eigenvectors associado ao eigenvalues superior da matriz de covariância de dados. 

Para cada nova entrada, o detector de anomalias primeiro computa sua projeção no eigenvectors e, em seguida, computa o erro de reconstrução normalizado. Esse erro é a pontuação de anomalias. Quanto maior o erro, mais anômala é a instância. Para obter detalhes sobre como o espaço normal é computado, consulte Wikipédia: [análise de componente principal](https://wikipedia.org/wiki/Principal_component_analysis). 


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

Consulte [exceções e códigos de erro para o designer](designer-error-codes.md) para obter uma lista de erros específicos para os módulos do designer.