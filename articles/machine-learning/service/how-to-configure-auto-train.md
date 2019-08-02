---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning service
description: O aprendizado de máquina automatizado escolhe um algoritmo para você e gera um modelo pronto para implantação. Saiba quais opções você pode usar para configurar experimentos de aprendizado de máquina automatizados.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3a316de54600d18f7ab839b8459bfe4eb0ff86e8
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479787"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar experimentos de ML automatizados em Python

Neste guia, saiba como definir várias definições de configuração de seus experimentos de aprendizado de máquina automatizados com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados.

Para exibir exemplos de experimentos de aprendizado de máquina automatizados, confira [Tutorial: Treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [Treinar modelos com aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione o tipo de experimento: Previsão de classificação, regressão ou série temporal
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

Se preferir uma experiência sem código, você também poderá [criar experiências automatizadas de aprendizado de máquina no portal do Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O aprendizado de máquina automatizado dá suporte a tipos de tarefa de classificação, regressão e previsão.

O aprendizado de máquina automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo. 

Classificação | Regressão | Previsão de série temporal
|-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)
[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC (Classificação de vetores de suporte do C)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Use o `task` parâmetro `AutoMLConfig` no construtor para especificar o tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato
O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos nos formatos de dados compatíveis com scikit-learn. É possível ler os dados para:
* Matrizes numpy X (recursos) e y (variável de destino ou também conhecido como rótulo)
* Dataframe do Pandas

Exemplos:

*   Matrizes Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Dataframe do Pandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Buscar dados para executar o experimento em computação remota

Para execuções remotas, você precisa tornar os dados acessíveis da computação remota. Isso pode ser feito por meio do carregamento dos dados no repositório de armazenamento.

Aqui está um exemplo de como `datastore`usar:

```python
    import pandas as pd
    from sklearn import datasets
    
    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-deprep-references"></a>Definir referências de despreparação

Defina X e y como referência de dprep, que será passado para o objeto de `AutoMLConfig` aprendizado de máquina automatizado semelhante ao seguinte:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))
    
    
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

Você pode especificar o treinamento separado e a validação definidos diretamente `AutoMLConfig` no método.

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. As pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (subamostra aleatória repetida)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. Monte Carlo não tem suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use o conjunto de dados de validação personalizada se a divisão aleatória não for aceitável, geralmente de data de série temporal ou dados desequilibrados. É possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir:
*   O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
*   Um computador remoto na nuvem – [Computação gerenciada do Azure Machine Learning](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de machine learning em clusters de máquinas virtuais do Azure.

Confira o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de notebooks com destinos de computação locais e remotos.

*   Um cluster Azure Databricks na sua assinatura do Azure. Você pode encontrar mais detalhes aqui- [configurar Azure Databricks cluster para o ml automatizado](how-to-configure-environment.md#azure-databricks)

Consulte o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) para ver os notebooks de exemplo com Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) para obter uma lista completa de parâmetros.

Eis alguns exemplos:

1.  Experimento de classificação usando AUC ponderada como a métrica principal com um tempo máximo de 12.000 segundos por iteração, com o experimento programado para terminar após 50 iterações e duas partições de validação cruzada.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Veja abaixo um exemplo de experimento de regressão programado para terminar após 100 iterações, em que cada iteração dura até 600 segundos com cinco partições de validação cruzada.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Os três valores `task` de parâmetro diferentes determinam a lista de algoritmos a serem aplicados.  Use os parâmetros `whitelist` ou `blacklist` para modificar ainda mais as iterações com os algoritmos disponíveis a serem incluídos ou excluídos. A lista de modelos com suporte pode ser encontrada na [classe SupportedAlgorithms](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Métrica principal
A métrica primária; conforme mostrado nos exemplos acima, determina a métrica a ser usada durante o treinamento do modelo para otimização. A métrica primária que você pode selecionar é determinada pelo tipo de tarefa que você escolher. Abaixo está uma lista de métricas disponíveis.

|Classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Pré-processamento de dados & personalização

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são [dimensionados e normalizados automaticamente](concept-automated-ml.md#preprocess) para ajudar os algoritmos a funcionar bem.  No entanto, você também pode habilitar o pré-processamento/personalização adicional, como valores ausentes de imputação, codificação e transformações. [Saiba mais sobre o que o personalização está incluído](how-to-create-portal-experiments.md#preprocess).

Para habilitar esse personalização, especifique `"preprocess": True` para a [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Previsão de série temporal
Para o tipo de tarefa previsão de série temporal, você tem parâmetros adicionais para definir.
1. time_column_name-este é um parâmetro necessário que define o nome da coluna nos dados de treinamento que contém a série de data/hora.
1. max_horizon-define o período de tempo que você deseja prever com base na periodicidade dos dados de treinamento. Por exemplo, se você tiver dados de treinamento com refinamentos diários de tempo, defina a distância em dias que deseja que o modelo treine.
1. grain_column_names-define o nome das colunas que contêm dados de série temporal individuais em seus dados de treinamento. Por exemplo, se você estiver prevendo as vendas de uma determinada marca por loja, você definirá as colunas de armazenamento e marca como suas colunas de refinamento.

Veja o exemplo dessas configurações que estão sendo usadas abaixo. o exemplo de notebook está disponível [aqui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

## <a name="run-experiment"></a>Executar o experimento

Para o ml automatizado, você precisará `Experiment` criar um objeto, que é um objeto nomeado `Workspace` em um usado para executar experimentos.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
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

### <a name="exit-criteria"></a>Critérios de saída
Há algumas opções que você pode definir para concluir o experimento.
1. Sem critérios – se você não definir nenhum parâmetro de saída, o experimento continuará até que nenhum progresso adicional seja feito em sua métrica primária.
1. Número de iterações – você define o número de iterações para a execução do experimento. Você pode adicionar iteration_timeout_minutes opcionalmente para definir um limite de tempo em minutos por cada iteração.
1. Sair após um período de tempo – usando experiment_timeout_minutes em suas configurações, você pode definir por quanto tempo em minutos um experimento continuará em execução.
1. Sair depois que uma pontuação for atingida-usando experiment_exit_score, você poderá optar por concluir o experimento depois que uma pontuação baseada em sua métrica primária for atingida.

### <a name="explore-model-metrics"></a>Explorar as métricas do modelo

Você pode exibir os resultados de treinamento em um widget ou embutido se estiver em um bloco de anotações. Confira [Track and evaluate models](how-to-track-experiments.md#view-run-details) (Rastrear e avaliar modelos) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Entender os modelos de ML automatizados

Qualquer modelo produzido usando o ML automatizado inclui as seguintes etapas:
+ Engenharia automatizada de recursos (se pré-processar = true)
+ Dimensionamento/normalização e algoritmo com valores de hipermedidor

Tornamos transparente para obter essas informações da saída do fitted_model do ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia automatizada de recursos

Consulte a lista de pré-processamento e [engenharia de recursos automatizada](concept-automated-ml.md#preprocess) que ocorre quando pré-processamento = verdadeiro.

Considere este exemplo:
+ Há quatro recursos de entrada: A (numérico), B (numérico), C (numérico), D (DateTime)
+ O recurso numérico C é Descartado porque é uma coluna de ID com todos os valores exclusivos
+ Os recursos numéricos A e B têm valores ausentes e, portanto, são imputados por média
+ O recurso DateTime D é destacados em 11 recursos de engenharia diferentes

Use essas duas APIs na primeira etapa do modelo ajustado para entender mais.  Consulte [este bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retorna uma lista de nomes de recursos com engenharia.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Essa lista inclui todos os nomes de recursos de engenharia.

  >[!Note]
  >Use ' timeseriestransformer ' para a tarefa = ' previsão ', caso contrário use ' transtransformer ' para a tarefa ' regressão ' ou ' classificação '.

+ API 2: `get_featurization_summary()` retorna o resumo de personalização para todos os recursos de entrada.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Use ' timeseriestransformer ' para a tarefa = ' previsão ', caso contrário use ' transtransformer ' para a tarefa ' regressão ' ou ' classificação '.

  Saída:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Sendo que:

   |Saída|Definição|
   |----|--------|
   |RawFeatureName|Nome de recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Tipo de dados detectado do recurso de entrada.|
   |Passou|Indica se o recurso de entrada foi descartado ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos de engenharia.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Dimensionamento/normalização e algoritmo com valores de hipermedidor:

Para entender os valores de dimensionamento/normalização e de algoritmo/hiperparâmetro para um pipeline, use fitted_model. Steps. [Saiba mais sobre dimensionamento/normalização](concept-automated-ml.md#preprocess). Aqui está uma amostra de saída:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, use essa função auxiliar mostrada neste [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Veja a seguir um exemplo de saída para um pipeline usando um algoritmo específico (LogisticRegression com RobustScalar, neste caso).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Explicar o modelo (interpretabilidade)

O aprendizado de máquina automatizado permite que você entenda a importância do recurso.  Durante o processo de treinamento, é possível obter a importância global do recurso para o modelo.  Para cenários de classificação, também é possível obter a importância do recurso no nível da classe.  É necessário fornecer um conjunto de dados de validação (X_valid) para obter a importância do recurso.

Há duas maneiras de gerar a importância do recurso.

*   Quando um experimento é concluído, é possível usar o método `explain_model` em qualquer iteração.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Para exibir a importância do recurso para todas as iterações, defina o sinalizador `model_explainability` como `True` em AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Quando terminar, será possível usar o método retrieve_model_explanation para recuperar a importância do recurso para uma iteração específica.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

É possível visualizar o gráfico de importância do recurso em seu workspace no portal do Azure. Exiba a URL usando o objeto de execução:

```
automl_run.get_portal_url()
```

É possível visualizar o gráfico de importância do recurso em seu workspace no portal do Azure. O gráfico também é mostrado ao usar o `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) em um bloco de anotações. Para saber mais sobre os gráficos, consulte [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![gráfico de importância do recurso](./media/how-to-configure-auto-train/feature-importance.png)

Para obter mais informações sobre como as explicações de modelo e a importância do recurso podem ser habilitadas em outras áreas do SDK fora do aprendizado de máquina automatizado, consulte o artigo [conceito](machine-learning-interpretability-explainability.md) sobre interpretação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).
