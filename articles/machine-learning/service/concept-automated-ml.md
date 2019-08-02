---
title: O que é o ML/automl automatizado
titleSuffix: Azure Machine Learning service
description: Saiba como Serviço do Azure Machine Learning pode escolher um algoritmo para você e automaticamente gerar um modelo proveniente para economizar tempo usando os parâmetros e os critérios que você fornecer ao selecionar o melhor algoritmo para seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: c563278a9d23810a5e6f0adc8082c8cfc5a0510c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358859"
---
# <a name="what-is-automated-machine-learning"></a>O que é o aprendizado de máquina automatizado?

O aprendizado de máquina automatizado, também conhecido como autoML, é o processo de automatizar as tarefas iterativas demoradas do desenvolvimento de modelos de aprendizado de máquina. Ele permite que cientistas de dados, analistas e desenvolvedores criem modelos de ML com alta escala, eficiência e produtividade, tudo ao mesmo tempo em que sustenta a qualidade do modelo.

O desenvolvimento de modelos de aprendizado de máquina tradicional tem uso intensivo de recursos, exigindo conhecimento e tempo de domínio significativos para produzir e comparar dezenas de modelos. Aplique ML automatizado quando desejar que Azure Machine Learning treine e ajuste um modelo para você usando a métrica de destino que você especificar. Em seguida, o serviço faz a iteração por meio de algoritmos de ML emparelhados com seleções de recursos, em que cada iteração produz um modelo com uma pontuação de treinamento. Quanto maior a pontuação, melhor o modelo é considerado para "ajustar" seus dados.

Com o aprendizado de máquina automatizado, você acelerará o tempo necessário para obter modelos de ML prontos para produção com grande facilidade e eficiência.

## <a name="when-to-use-automated-ml"></a>Quando usar ML automatizado

Democratiza automatizado o processo de desenvolvimento do modelo do Machine Learning e capacita seus usuários, não importa sua experiência em ciência de dados, para identificar um pipeline de aprendizado de máquina de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores em setores podem usar ML automatizado para:

+ Implementar soluções de Machine Learning sem amplo conhecimento de programação
+ Economize tempo e recursos
+ Aproveitar as práticas recomendadas de ciência de dados
+ Fornecer solução de problemas ágil

## <a name="how-automated-ml-works"></a>Como o ML automatizado funciona

Usando **Azure Machine Learning Service**, você pode projetar e executar seus experimentos de treinamento de ml automatizados com estas etapas:

1. **Identificar o problema de ml** a ser resolvido: classificação, previsão ou regressão

1. **Especifique a origem e o formato dos dados de treinamento rotulados**: Matrizes numpy ou pandas dataframe

1. **Configure o destino de computação para treinamento de modelo**, como seu [computador local, Azure Machine Learning computações, VMS remotas ou Azure Databricks](how-to-set-up-training-targets.md).  Saiba mais sobre o treinamento automatizado [em um recurso remoto](how-to-auto-train-remote.md).

1. **Configure os parâmetros automatizados de aprendizado de máquina** que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetro, pré-processamento avançado/personalização e quais métricas examinar ao determinar o melhor modelo.  Você pode definir as configurações para o teste de treinamento automático [no portal do Azure](how-to-create-portal-experiments.md) ou [com o SDK](how-to-configure-auto-train.md).

1. **Envie a execução de treinamento.**

  ![Machine Learning automatizado](./media/how-to-automated-ml/automl-concept-diagram2.png)

Durante o treinamento, o serviço de Azure Machine Learning cria um número de pipelines paralelos que tentam algoritmos e parâmetros diferentes. Ele será interrompido quando atingir os critérios de saída definidos no experimento.

Você também pode inspecionar as informações de execução registradas, que [contêm](how-to-understand-automated-ml.md) as métricas coletadas durante a execução. A execução de treinamento produz um objeto serializado do`.pkl` Python (arquivo) que contém o modelo e o pré-processamento de dados.

Embora a criação de modelos seja automatizada, você também pode [aprender como os recursos importantes ou relevantes são](how-to-configure-auto-train.md#explain) para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pré-processamento

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são pré-processados usando os métodos padrão e, opcionalmente, por meio de pré-processamento avançado.

### <a name="automatic-preprocessing-standard"></a>Pré-processamento automático (padrão)

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são dimensionados e normalizados automaticamente para ajudar os algoritmos a executarem bem.  Durante o treinamento do modelo, uma das técnicas de dimensionamento ou normalização a seguir será aplicada a cada modelo.

|Dimensionamento&nbsp;&denormalização&nbsp;| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar recursos removendo a média e dimensionamento para a variação de unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma recursos dimensionando cada recurso pelo mínimo e máximo da coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Dimensionar cada recurso por seu valor absoluto máximo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Esses recursos de scaler por seu intervalo de Quantil |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução de dimensionalidade linear usando a decomposição de valor singular dos dados para projetar a ti em um espaço dimensional inferior |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Esse transformador executa a redução de dimensionalidade linear por meio da decomposição de valor singular truncado (SVD). Ao contrário do PCA, esse estimador não centraliza os dados antes de calcular a decomposição do valor singular. Isso significa que ele pode trabalhar com matrizes SciPy. esparsas com eficiência |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada exemplo (ou seja, cada linha da matriz de dados) com pelo menos um componente diferente de zero é redimensionado independentemente de outros exemplos para que seu normal (L1 ou L2) seja igual a um |

### <a name="advanced-preprocessing-optional-featurization"></a>Pré-processamento avançado: personalização opcional

O pré-processamento avançado adicional e os personalização também estão disponíveis, como valores ausentes de imputação, codificação e transformações. [Saiba mais sobre o que o personalização está incluído](how-to-create-portal-experiments.md#preprocess). Habilite essa configuração com:

+ Portal do Azure: Marcando a caixa de seleção **pré-processar** nas **Configurações avançadas** [com estas etapas](how-to-create-portal-experiments.md).

+ SDK do Python: Especificando `"preprocess": True` para [ a`AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Previsão de série temporal
A criação de previsões é parte integrante de qualquer empresa, seja ela de receita, inventário, vendas ou demanda do cliente. Você pode usar o ML automatizado para combinar técnicas e abordagens e obter uma previsão de série temporal de alta qualidade e recomendada. 

Um experimento de série temporal automatizado é tratado como um problema de regressão multivariado. Os valores de série temporal anteriores são "dinamizados" para se tornarem dimensões adicionais para o regressor junto com outros previsões. Essa abordagem, ao contrário dos métodos de série temporal clássica, tem uma vantagem de incorporar naturalmente várias variáveis contextuais e sua relação entre si durante o treinamento. O ML automatizado aprende um modelo único, mas geralmente ramificado internamente para todos os itens no conjunto de e horizontes de previsão. Por isso, mais dados estão disponíveis para estimar os parâmetros de modelo e a generalização para uma série não vista se torna possível. 

Saiba mais e veja um exemplo de [Machine Learning automatizado para previsão de série temporal](how-to-auto-train-forecast.md).

## <a name="ensemble-models"></a>Modelos de Ensemble

Você pode treinar modelos Ensemble usando o Machine Learning automatizado com o [algoritmo de seleção Caruana Ensemble com a inicialização de Ensemble classificada](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). O Ensemble Learning melhora os resultados do Machine Learning e o desempenho de previsão, combinando muitos modelos em oposição ao uso de modelos únicos. A iteração Ensemble aparece como a última iteração de sua execução.

## <a name="use-with-onnx-in-c-apps"></a>Usar com ONNX em C# aplicativos

Com Azure Machine Learning, você pode usar o ML automatizado para criar um modelo Python e fazer com que ele seja convertido no formato ONNX. O tempo de execução C#do ONNX dá suporte para que você possa usar o modelo C# criado automaticamente em seus aplicativos sem a necessidade de recodificar ou qualquer uma das latências de rede que os pontos de extremidade REST apresentam. Experimente um exemplo desse fluxo [neste Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>ML automatizado na Microsoft

O ML automatizado também está disponível em outras soluções da Microsoft, como:

|Integrações|Descrição|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Seleção automática de modelos e treinamento em aplicativos .NET usando o Visual Studio e Visual Studio Code com o ML automatizado ML.NET (versão prévia).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Escale horizontalmente seus trabalhos de treinamento de ML automatizados no Spark em clusters HDInsight em paralelo.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Invocar modelos de aprendizado de máquina diretamente no Power BI (versão prévia).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Crie novos modelos de Machine Learning em seus dados em clusters SQL Server 2019 Big Data.|

## <a name="next-steps"></a>Próximas etapas

Veja exemplos e aprenda a criar modelos usando o aprendizado de máquina automatizado:

+ Siga o [tutorial: Treinar automaticamente um modelo de regressão com o Machine Learning automatizado do Azure](tutorial-auto-train-models.md)

+ Defina as configurações para o teste de treinamento automático:
  + Na interface portal do Azure, [Use estas etapas](how-to-create-portal-experiments.md).
  + Com o SDK do Python, [Use estas etapas](how-to-configure-auto-train.md).

+ Saiba como treinar automaticamente usando dados de série temporal, [Use estas etapas](how-to-auto-train-forecast.md).

+ Experimente [Jupyter Notebook amostras para o aprendizado de máquina automatizado](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
