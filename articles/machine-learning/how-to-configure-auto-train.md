---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning
description: Saiba como definir fontes de dados, cálculos e definições de configuração para seus experimentos de aprendizado de máquina automatizados.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 6971d67204beb39ff0afa6c68dbecf278d86b299
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954708"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizado no Python


Neste guia, vamos mostrar como definir várias configurações dos seus experimentos de machine learning automatizado com o [SDK do Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados.

Para exibir exemplos de experimentos de aprendizado de máquina automatizados, consulte [tutorial: treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [treinar modelos com o aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione o tipo de experimento: Classificação, regressão ou previsão de série temporal
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

Se preferir um experimento sem código, você também pode [Criar seus experimentos de machine learning automatizado no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa, 
* Um Workspace do Azure Machine Learning. Para criar o workspace, consulte [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

* O SDK do Azure Machine Learning Python instalado.
    Para instalar o SDK, você pode, 
    * Crie uma instância de computação, que instala automaticamente o SDK e é pré-configurado para fluxos de trabalho de ML. Consulte [criar e gerenciar uma instância de computação Azure Machine Learning](how-to-create-manage-compute-instance.md) para obter mais informações. 

    * [Instale o `automl` pacote](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), que inclui a [instalação padrão](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install) do SDK.

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O Machine Learning automatizado oferece suporte a tipos de tarefas do `classification` , `regression` e `forecasting` . Saiba mais sobre [tipos de tarefas](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

O código a seguir usa o `task` parâmetro no `AutoMLConfig` construtor para especificar o tipo de experimento como `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato

O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos em um **DataFrame do Pandas** ou em um **TabularDataset do Azure Machine Learning**. [Saiba mais sobre conjuntos de dados](how-to-create-register-datasets.md).

Requisitos para dados de treinamento no Machine Learning:
- Os dados devem estar em formato de tabela.
- O valor que você quer prever, coluna de destino, deve estar presente nos dados.

**Para experimentos remotos**, os dados de treinamento devem ser acessíveis a partir da computação remota. O AutoML só aceita [TabularDatasets do Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) ao trabalhar em uma computação remota. 

Os conjuntos de dados do Azure Machine Learning expõem a funcionalidade para:

* Transfira facilmente dados de arquivos estáticos ou de fontes de URL para seu espaço de trabalho.
* Disponibilizar dados para scripts de treinamento ao executar recursos de computação em nuvem. Confira [como treinar com conjuntos](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) de dados para obter um exemplo de como usar a `Dataset` classe para montar o seu destino de computação remota.

O código a seguir cria um TabularDataset de uma URL da Web. Consulte [criar um TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) para obter exemplos de código sobre como criar conjuntos de itens de outras fontes, como arquivos locais e repositórios de armazenamento.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Para experimentos de computação local**, recomendamos o pandas dataframes para tempos de processamento mais rápidos.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Dados de treinamento, validação e teste

Você pode especificar **dados de treinamento e conjuntos de dados de validação** separados diretamente no `AutoMLConfig` Construtor. Saiba mais sobre [como configurar divisões de dados e validação cruzada](how-to-configure-cross-validation-data-splits.md) para seus experimentos de AutoML. 

Se você não especificar explicitamente um `validation_data` parâmetro ou `n_cross_validation` , o ml automatizado aplicará técnicas padrão para determinar como a validação é executada. Essa determinação depende do número de linhas no conjunto de registros atribuído ao seu `training_data` parâmetro. 

|&nbsp;Tamanho dos dados de treinamento &nbsp;| Técnica de validação |
|---|-----|
|**Mais &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A divisão de dados de treinamento/validação é aplicada. O padrão é levar 10% do conjunto de dados de treinamento inicial como o conjunto de validação. Por sua vez, esse conjunto de validação é usado para cálculo de métricas.
|**Menos &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A abordagem de validação cruzada é aplicada. O número padrão de dobras depende do número de linhas. <br> **Se o conjunto de registros for menor que 1.000 linhas**, serão usadas 10 dobras. <br> **Se as linhas estiverem entre 1.000 e 20.000**, serão usadas três dobras.

Neste momento, você precisa fornecer seus próprios dados de **teste** para avaliação do modelo. Para obter um exemplo de código de como trazer seus próprios dados de teste para avaliação de modelo, consulte a seção de **teste** deste [Notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir. Conheça os [prós e contras das opções de computação local e remota](concept-automated-ml.md#local-remote). 

* Seu computador **local** , como uma área de trabalho local ou um laptop, geralmente quando você tem um pequeno conjunto de um e ainda está no estágio de exploração. Consulte [este notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) para obter um exemplo de computação local. 
 
* Um computador **remoto** na nuvem – [Azure Machine Learning computação gerenciada](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de aprendizado de máquina em clusters de máquinas virtuais do Azure. 

    Consulte [este notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) para obter um exemplo remoto usando computação gerenciada do Azure Machine Learning. 

* Um **cluster Azure Databricks** na sua assinatura do Azure. Você pode encontrar mais detalhes em [configurar um cluster de Azure Databricks para o ml automatizado](how-to-configure-databricks-automl-environment.md). Consulte este [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para obter exemplos de notebooks com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1. Experimento de classificação usando AUC ponderado como a métrica primária com tempo limite de experimento definido como 30 minutos e 2 dobras de validação cruzada.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. O exemplo a seguir é um experimento de regressão definido para terminar após 60 minutos com cinco dobras cruzadas de validação.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Tarefas de previsão exigem configuração extra, consulte o artigo [treinar o modelo de previsão de série temporal](how-to-auto-train-forecast.md) para obter mais detalhes. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Modelos com suporte

O aprendizado de máquina automatizado tenta modelos e algoritmos diferentes durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo. 

Os três `task` valores de parâmetro diferentes determinam a lista de algoritmos ou modelos a serem aplicados. Use os parâmetros `allowed_models` ou `blocked_models` para modificar ainda mais as iterações com os modelos disponíveis a serem incluídos ou excluídos. 

A tabela a seguir resume os modelos com suporte por tipo de tarefa. 

> [!NOTE]
> Se você planeja exportar seus modelos de machine learning automatizado para um [modelo de ONNX](concept-onnx.md), somente os algoritmos indicados com um * podem ser convertidos no formato ONNX. Saiba mais sobre [conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observe também que, no momento, o ONNX tem suporte apenas a tarefas de classificação e regressão. 

classificação | Regressão | Previsão de série temporal
|-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)* |[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)*|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)
[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC (Classificação de vetores de suporte)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador do Perceptron médio](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Regressor descendente do gradiente online](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Regressor linear rápido](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Classificador SVM linear](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Métrica principal
O `primary metric` parâmetro determina a métrica a ser usada durante o treinamento do modelo para otimização. As métricas disponíveis para seleção são determinadas pelo tipo de tarefa que você escolher, e a tabela a seguir mostra métricas primárias válidas para cada tipo de tarefa.

A escolha de uma métrica primária para o aprendizado de máquina automatizado para otimizar depende de muitos fatores. Recomendamos que a sua consideração principal seja escolher uma métrica que melhor represente suas necessidades de negócios. Em seguida, considere se a métrica é adequada para o seu perfil de conjunto de dados (tamanho, intervalo, distribuição de classe etc.).

Saiba mais sobre as definições específicas dessas métricas em [Entender os resultados do machine learning automatizado](how-to-understand-automated-ml.md).

|classificação | Regressão | Previsão de série temporal
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Métricas primárias para cenários de classificação 

Publique as métricas decorrentes, como,, `accuracy` `average_precision_score_weighted` `norm_macro_recall` e `precision_score_weighted` não pode ser otimizado também para conjuntos de os que são muito pequenos, têm uma distorção de classe muito grande (desequilíbrio de classe) ou quando o valor de métrica esperado é muito próximo a 0,0 ou 1,0. Nesses casos, `AUC_weighted` pode ser uma opção melhor para a métrica primária. Após a conclusão do Machine Learning automatizado, você pode escolher o modelo vencedor baseado na métrica mais adequada para suas necessidades de negócios.

| Métrica | Caso (s) de uso de exemplo |
| ------ | ------- |
| `accuracy` | Classificação de imagem, análise de sentimentos, previsão de rotatividade |
| `AUC_weighted` | Detecção de fraudes, classificação de imagem, detecção de anomalias/detecção de spam |
| `average_precision_score_weighted` | Análise de sentimento |
| `norm_macro_recall` | Previsão de variação |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Métricas primárias para cenários de regressão

Métricas como `r2_score` e `spearman_correlation` podem representar melhor a qualidade do modelo quando a escala do valor a prever abrange muitas ordens de magnitude. Por exemplo, estimativa de salário, em que muitas pessoas têm um salário de US $20 mil a US $100 mil, mas a escala é muito alta com alguns salários no intervalo de $100 milhões. 

`normalized_mean_absolute_error` e, `normalized_root_mean_squared_error` nesse caso, tratará um erro de previsão de $20 mil, o mesmo para um trabalho com um salário $30 mil como um trabalho fazendo $20 milhões. Na realidade, a previsão de apenas $20 mil de um salário de $20 milhões é muito próxima (uma pequena diferença relativa de 0,1%), enquanto $20 mil de $30 mil não está próximo (uma grande diferença relativa a 67%). `normalized_mean_absolute_error` e `normalized_root_mean_squared_error` são úteis quando os valores a prever estão em uma escala semelhante.

| Métrica | Caso (s) de uso de exemplo |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Previsão de preço (casa/produto/gorjeta), revisar previsão de Pontuação |
| `r2_score` | Atraso de companhia aérea, estimativa de salário, tempo de resolução de bug |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Métricas primárias para cenários de previsão de série temporal

Consulte as notas de regressão acima.

| Métrica | Caso (s) de uso de exemplo |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Previsão de preço (previsão), otimização de estoque, previsão de demanda |
| `r2_score` | Previsão de preço (previsão), otimização de estoque, previsão de demanda |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Definição de recursos de dados

Em todos os experimentos de machine learning automatizado, seus dados são automaticamente escalados e normalizados para ajudar *determinados* algoritmos sensíveis a recursos que estão em escalas diferentes. Esse dimensionamento e normalização são chamados de personalização. Consulte [personalização em AutoML](how-to-configure-auto-features.md#) para obter mais detalhes e exemplos de código. 

Ao configurar seus experimentos em seu `AutoMLConfig` objeto, você pode habilitar/desabilitar a configuração `featurization` . A tabela a seguir mostra as configurações aceitas para personalização no [objeto AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configuração de definição de recursos | Descrição |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica que, como parte do pré-processamento, [verificadores de integridade dos dados e etapas de definição de recursos](how-to-configure-auto-features.md#featurization) são executados automaticamente. **Configuração padrão**.|
|`"featurization": 'off'`| Indica que a etapa personalização não deve ser feita automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica que uma etapa de definição de recursos personalizada deve ser usada. [Saiba como personalizar a definição de recursos](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> As etapas de definição de recursos de machine learning automatizado (normalização de recursos, manipulação de dados ausentes, conversão de texto em números, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de definição de recursos aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Configuração do Ensemble

Os modelos Ensemble são habilitados por padrão e aparecem como as iterações de execução final em uma execução AutoML. Atualmente, há suporte para **VotingEnsemble** e **StackEnsemble** . 

A votação implementa a votação reversível, que usa médias ponderadas. A implementação de empilhamento usa uma implementação de duas camadas, em que a primeira camada tem os mesmos modelos que o Ensemble de votação, e o segundo modelo de camada é usado para localizar a combinação ideal dos modelos da primeira camada. 

Se você estiver usando modelos ONNX **ou** tiver a explicação de modelo habilitado, o empilhamento será desabilitado e apenas a votação será utilizada.

O treinamento do Ensemble pode ser desabilitado usando os `enable_voting_ensemble` `enable_stack_ensemble` parâmetros Boolianos e.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Para alterar o comportamento padrão de Ensemble, há vários argumentos padrão que podem ser fornecidos como `kwargs` em um `AutoMLConfig` objeto.

> [!IMPORTANT]
>  Os parâmetros a seguir não são parâmetros explícitos da classe AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Durante **VotingEnsemble** e geração de modelo de **StackEnsemble**, vários modelos ajustados das execuções filho anteriores são baixados. Se você encontrar esse erro: `AutoMLEnsembleException: Could not find any models for running ensembling`, talvez seja necessário esperar mais para que os modelos sejam baixados. O valor padrão é 300 segundos para baixar esses modelos em paralelo e não o tempo limite máximo não é limitado. Configure esse parâmetro com um valor maior que 300 segundos, se for necessário mais tempo. 

  > [!NOTE]
  >  Se o tempo limite for atingido e houver modelos baixados, o ensembling continuará com quantos modelos foram baixados. Não é necessário que todos os modelos sejam baixados para concluir dentro desse tempo limite.

Os parâmetros a seguir se aplicam somente a modelos de **StackEnsemble**: 

* `stack_meta_learner_type`: o meta-aprendiz é um modelo treinado na saída dos modelos heterogêneos individuais. Os meta-aprendizes padrão são `LogisticRegression` para tarefas de classificação (ou `LogisticRegressionCV` se a validação cruzada estiver habilitada) e `ElasticNet` para tarefas de regressão/previsão (ou `ElasticNetCV` se a validação cruzada estiver habilitada). Esse parâmetro pode ser uma das seguintes cadeias de caracteres: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` ou `LinearRegression`.

* `stack_meta_learner_train_percentage`: especifica a proporção do conjunto de treinamento (ao escolher treinar e tipo de validação de treinamento) a ser reservado para treinar o meta-aprendiz. O valor padrão é `0.2`. 

* `stack_meta_learner_kwargs`: parâmetros opcionais a serem passados para o inicializador do meta-aprendiz. Esses parâmetros e tipos de parâmetro espelham os parâmetros e os tipos de parâmetro do construtor de modelo correspondente e são encaminhados para o construtor de modelo.

O código a seguir mostra um exemplo de especificação do comportamento de ensemble personalizado em um objeto `AutoMLConfig`.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Critérios de saída

Há algumas opções que você pode definir em seu AutoMLConfig para encerrar seu experimento.

|Critérios| descrição
|----|----
Nenhum &nbsp; critério | Se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum outro progresso seja feito em sua métrica primária.
Após &nbsp; um &nbsp; período &nbsp; de &nbsp; tempo| Use `experiment_timeout_minutes` em suas configurações para definir por quanto tempo, em minutos, seu experimento deve continuar a ser executado. <br><br> Para ajudar a evitar falhas de tempo limite de experimento, há um mínimo de 15 minutos ou 60 minutos se a linha por tamanho de coluna exceder 10 milhões.
Uma &nbsp; Pontuação &nbsp; foi &nbsp; &nbsp; atingida| Usar `experiment_exit_score` concluirá o experimento depois que uma pontuação de métrica primária especificada for atingida.

## <a name="run-experiment"></a>Executar o experimento

Para ML automatizado, você pode criar um objeto `Experiment`, que é um objeto nomeado em um `Workspace` usado para executar experimentos.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Envie o experimento para ser executado e para gerar um modelo. Passe o `AutoMLConfig` para o método `submit` para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez em um novo computador.  Pode levar até dez minutos antes da saída ser exibida.
>Definir `show_output` como `True` faz a saída ser mostrada no console.

### <a name="multiple-child-runs-on-clusters"></a>Várias execuções filhas em clusters

Execuções filhas de teste ML automatizadas podem ser executadas em um cluster que já está executando outro experimento. No entanto, o tempo depende de quantos nós o cluster tem e se esses nós estão disponíveis para executar um experimento diferente.

Cada nó no cluster atua como uma VM (máquina virtual) individual que pode realizar uma única execução de treinamento; para o ML automatizado, isso significa uma execução filho. Se todos os nós estiverem ocupados, o novo experimento será colocado na fila. Mas, se houver nós livres, o novo experimento executará o filho de ML automatizado em paralelo nos nós/VMs disponíveis.

Para ajudar a gerenciar as execuções filhas e quando elas podem ser executadas, recomendamos que você crie um cluster dedicado por experimento e coincida com o número de `max_concurrent_iterations` seu experimento para o número de nós no cluster. Dessa forma, você usa todos os nós do cluster ao mesmo tempo com o número de execuções/iterações de filhos simultâneas desejadas.

Configure  `max_concurrent_iterations` em seu `AutoMLConfig` objeto. Se não estiver configurado, por padrão, apenas uma execução/iteração filho simultânea é permitida por experimento.  

## <a name="explore-models-and-metrics"></a>Explorar modelos e métricas

Será possível exibir os resultados de treinamento em um widget ou embutidos se você estiver usando um notebook. Confira [Track and evaluate models](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) (Rastrear e avaliar modelos) para obter mais detalhes.

Consulte [avaliar os resultados do experimento automatizado do Machine Learning](how-to-understand-automated-ml.md) para obter definições e exemplos dos gráficos de desempenho e métricas fornecidos para cada execução. 

Para obter um resumo do personalização e entender quais recursos foram adicionados a um modelo específico, consulte [transparência do personalização](how-to-configure-auto-features.md#featurization-transparency). 

> [!NOTE]
> Os algoritmos Automated ML empregam uma aleatoriedade inerente que pode causar uma pequena variação em uma pontuação de métrica final do modelo recomendado, como a precisão. O ML automatizado também realiza operações em dados como o Train-Test Split, a divisão de treinamento ou a validação cruzada quando necessário. Portanto, se você executar um experimento com as mesmas definições de configuração e métrica primária várias vezes, provavelmente verá variação em cada experimento a pontuação de métricas finais devido a esses fatores. 

## <a name="register-and-deploy-models"></a>Registrar e implantar modelos

Para obter detalhes sobre como baixar ou registrar um modelo para implantação em um serviço Web, consulte [como e onde implantar um modelo](how-to-deploy-and-where.md).

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilidade do modelo

A interpretabilidade de modelo permite entender por que seus modelos fizeram previsões e os valores de importância do recurso subjacentes. O SDK inclui vários pacotes para habilitar recursos de interpretação de modelo, tanto no tempo de inferência quanto no treinamento, para modelos locais e implantados.

Consulte [instruções](how-to-machine-learning-interpretability-automl.md) para ver exemplos de código para habilitar recursos de interpretação especificamente em experimentos de machine learning automatizado.

Para obter informações gerais sobre como as explicações de modelo e a importância do recursos podem ser habilitadas em outras áreas do SDK fora do machine learning automatizado, consulte o artigo [conceito](how-to-machine-learning-interpretability.md) sobre a interpretabilidade.

> [!NOTE]
> O modelo ForecastTCN não tem suporte no momento pelo cliente de explicação. Esse modelo não retornará um painel de explicação se ele for retornado como o melhor modelo e não oferecer suporte a execuções de explicação sob demanda.

## <a name="troubleshooting"></a>Solução de problemas

* A **atualização recente de `AutoML` dependências para versões mais recentes irá perder a compatibilidade**: a partir da versão 1.13.0 do SDK, os modelos não serão carregados em SDKs mais antigos devido à incompatibilidade entre as versões mais antigas que nós fixamos em nossos pacotes anteriores e as versões mais recentes que nós fixarmos agora. Você verá um erro como:
  * Módulo não encontrado: ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Erros de importação: ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Erros de atributo: ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Para contornar esse problema, execute uma das duas etapas a seguir, dependendo da sua `AutoML` versão de treinamento do SDK:
    * Se sua `AutoML` versão de treinamento do SDK for maior que 1.13.0, você precisará de `pandas == 0.25.1` e `sckit-learn==0.22.1` . Se houver uma incompatibilidade de versão, atualize scikit-Learn e/ou pandas para corrigir a versão, conforme mostrado abaixo:
      
      ```bash
         pip install --upgrade pandas==0.25.1
         pip install --upgrade scikit-learn==0.22.1
      ```
      
    * Se sua `AutoML` versão de treinamento do SDK for menor ou igual a 1.12.0, você precisará de `pandas == 0.23.4` e `sckit-learn==0.20.3` . Se houver uma incompatibilidade de versão, faça o downgrade de scikit-Learn e/ou pandas para corrigir a versão, conforme mostrado abaixo:
  
      ```bash
        pip install --upgrade pandas==0.23.4
        pip install --upgrade scikit-learn==0.20.3
      ```

* **Falha na implantação**: para versões <= 1.18.0 do SDK, a imagem base criada para implantação pode falhar com o seguinte erro: "ImportError: não é possível importar o nome `cached_property` de `werkzeug` ". 

  As etapas a seguir podem contornar o problema:
  1. Baixar o pacote de modelo
  2. Descompactar o pacote
  3. Implantar usando os ativos descompactados

* A **previsão da Pontuação do R2 é sempre zero**: esse problema ocorre se os dados de treinamento fornecidos tiverem uma série temporal que contém o mesmo valor para os últimos `n_cv_splits`  +  `forecasting_horizon` pontos de dados. Se esse padrão for esperado em sua série temporal, você poderá alternar a métrica primária para um erro de quadrado médio de raiz normalizado.
 
* **TensorFlow**: a partir da versão 1.5.0 do SDK, o Machine Learning automatizado não instala modelos TensorFlow por padrão. Para instalar o TensorFlow e usá-lo com seus experimentos de ML automatizados, instale TensorFlow = = 1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```

* **Gráficos de experimento**: os gráficos de classificação binária (recall de precisão, Roc, curva de lucro, etc.) mostrados em iterações de experimento de ml automatizadas não são renderizados corretamente na interface do usuário desde 4/12. Atualmente, as plotagens de gráfico estão mostrando resultados inversos, onde os modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

* **Databricks cancelar uma execução automatizada do Machine Learning**: quando você usa recursos automatizados de aprendizado de máquina no Azure Databricks, para cancelar uma execução e iniciar uma nova execução de experimento, reinicie o cluster Azure Databricks.

* **Databricks >10 iterações para o aprendizado de máquina automatizado**: em configurações automatizadas do Machine Learning, se você tiver mais de 10 iterações, defina `show_output` como `False` ao enviar a execução.

* **Widget do databricks para o SDK do Azure Machine Learning e o aprendizado de máquina automatizado**: o widget do sdk do Azure Machine Learning não tem suporte em um bloco de anotações do databricks porque os notebooks não podem analisar widgets HTML. Você pode exibir o widget no portal usando este código Python na célula Azure Databricks notebook:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup falha**: 
    * No Windows, execute automl_setup de um prompt do Anaconda. Use este link para [instalar o Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Verifique se o Conda 64-bit está instalado, em vez de 32 bits executando o `conda info` comando. O `platform` deve ser `win-64` para Windows ou `osx-64` para Mac.
    * Verifique se o Conda 4.4.10 ou posterior está instalado. Você pode verificar a versão com o comando `conda -V` . Se você tiver uma versão anterior instalada, poderá atualizá-la usando o comando: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Se o `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` erro for encontrado, instale o princípios de Build usando o comando `sudo apt-get install build-essential` .
      * Passe um novo nome como o primeiro parâmetro para automl_setup para criar um novo ambiente Conda. Exibir ambientes Conda existentes usando `conda env list` e removê-los com o `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh falhará**: se automl_setup_linus. sh falhar em Ubuntu Linux com o erro: `unable to execute 'gcc': No such file or directory`-
  1. Verifique se as portas de saída 53 e 80 estão habilitadas. Em uma VM do Azure, você pode fazer isso na portal do Azure selecionando a VM e clicando em rede.
  2. Execute o comando: `sudo apt-get update`
  3. Execute o comando: `sudo apt-get install build-essential --fix-missing`
  4. Executar `automl_setup_linux.sh` novamente

* **Configuration. ipynb falha**:
  * Para Conda locais, primeiro verifique se automl_setup foi executado com êxito.
  * Verifique se o subscription_id está correto. Localize os subscription_id na portal do Azure selecionando todos os serviços e, em seguida, assinaturas. Os caracteres "<" e ">" não devem ser incluídos no valor de subscription_id. Por exemplo, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` tem o formato válido.
  * Verifique se o acesso de colaborador ou proprietário à assinatura.
  * Verifique se a região é uma das regiões com suporte:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * Verifique o acesso à região usando o portal do Azure.
  
* **`import AutoMLConfig` falha**: houve alterações de pacote na versão do Machine Learning automatizada 1.0.76, que exigem que a versão anterior seja desinstalada antes de atualizar para a nova versão. Se o `ImportError: cannot import name AutoMLConfig` for encontrado após a atualização de uma versão do SDK antes de v 1.0.76 para v 1.0.76 ou posterior, resolva o erro executando: `pip uninstall azureml-train automl` e, em seguida `pip install azureml-train-auotml` . O script automl_setup. cmd faz isso automaticamente. 

* **Workspace.from_config falha**: se as chamadas ws = Workspace.from_config () ' falharem-
  1. Verifique se o bloco de anotações Configuration. ipynb foi executado com êxito.
  2. Se o bloco de anotações estiver sendo executado de uma pasta que não está sob a pasta em que o `configuration.ipynb` foi executado, copie a pasta aml_config e o config.jsde arquivo que ele contém para a nova pasta. Workspace.from_config lê o config.jsem para a pasta do bloco de anotações ou sua pasta pai.
  3. Se uma nova assinatura, um grupo de recursos, um espaço de trabalho ou uma região estiver sendo usada, certifique-se de executar o `configuration.ipynb` bloco de anotações novamente. Alterar config.jsdiretamente só funcionará se o espaço de trabalho já existir no grupo de recursos especificado na assinatura especificada.
  4. Se você quiser alterar a região, altere o espaço de trabalho, o grupo de recursos ou a assinatura. `Workspace.create` não criará ou atualizará um espaço de trabalho se ele já existir, mesmo se a região especificada for diferente.
  
* **Falha no bloco de anotações de exemplo**: se um bloco de anotações de exemplo falhar com um erro que a propriedade, o método ou a biblioteca não existe:
  * Verifique se o kernel correto foi selecionado na Jupyter Notebook. O kernel é exibido no canto superior direito da página do bloco de anotações. O padrão é azure_automl. O kernel é salvo como parte do bloco de anotações. Portanto, se você alternar para um novo ambiente Conda, terá que selecionar o novo kernel no bloco de anotações.
      * Por Azure Notebooks, ele deve ser Python 3,6. 
      * Para ambientes Conda locais, ele deve ser o nome do ambiente Conda que você especificou em automl_setup.
  * Verifique se o notebook é para a versão do SDK que você está usando. Você pode verificar a versão do SDK executando `azureml.core.VERSION` em uma célula Jupyter notebook. Você pode baixar a versão anterior dos blocos de anotações de exemplo do GitHub clicando no `Branch` botão, selecionando a `Tags` guia e, em seguida, selecionando a versão.

* **`import numpy` falha no Windows**: alguns ambientes do Windows veem um erro ao carregar o numpy com a versão mais recente do Python 3.6.8. Se você vir esse problema, tente com a versão 3.6.7 do Python.

* **`import numpy` falha**: Verifique a versão do TensorFlow no ambiente Conda do ml automatizado. As versões com suporte são < 1,13. Desinstale o TensorFlow do ambiente se a versão for >= 1,13. Você pode verificar a versão do TensorFlow e desinstalar da seguinte maneira:
  1. Inicie um shell de comando, ative o ambiente Conda onde os pacotes de ml automatizados são instalados.
  2. Insira `pip freeze` e procure `tensorflow` , se encontrado, a versão listada deve ser < 1,13
  3. Se a versão listada não for uma versão com suporte, `pip uninstall tensorflow` no Shell de comando e digite y para confirmação.
  
 * **A execução falha `jwt.exceptions.DecodeError` com**: mensagem de erro exata: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

    Para versões <= 1.17.0 do SDK, a instalação pode resultar em uma versão sem suporte do PyJWT. Verifique a versão do PyJWT no ambiente Conda do ml automatizado. As versões com suporte são < 2.0.0. Você pode verificar a versão do PyJWT da seguinte maneira:
    1. Inicie um shell de comando, ative o ambiente Conda onde os pacotes de ml automatizados são instalados.
    2. Insira `pip freeze` e procure `PyJWT` , se encontrado, a versão listada deve ser < 2.0.0

    Se a versão listada não for uma versão com suporte:
    1. Considere atualizar para a versão mais recente do SDK do AutoML: `pip install -U azureml-sdk[automl]` .
    2. Se isso não for viável, desinstale o PyJWT do ambiente e instale a versão correta da seguinte maneira:
        - `pip uninstall PyJWT` no Shell de comando e digite `y` para confirmação.
        - Instale o usando o `pip install 'PyJWT<2.0.0'` .

## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

+ Saiba mais sobre [como treinar um modelo de regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).

+ Saiba como treinar vários modelos com o AutoML no [acelerador de solução de vários modelos](https://aka.ms/many-models).
