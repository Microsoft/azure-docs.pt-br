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
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 33d8e18dcec98710443623c03651aa568aa37009
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819950"
---
# <a name="configure-automated-machine-learning-experiments"></a>Configurar experimentos de aprendizado de máquina automatizado

O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados. Neste guia, você aprende como definir várias configurações.

Para exibir exemplos de experimentos de aprendizado de máquina automatizados, confira [Tutorial: Treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [Treinar modelos com aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione o tipo de experimento: Classificação, regressão ou previsão da série temporal
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento
Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O aprendizado de máquina automatizado dá suporte a tipos de tarefa de classificação, regressão e previsão.

O aprendizado de máquina automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo. Embora os algoritmos DNN estejam disponíveis durante o treinamento, ML automatizado não compila modelos DNN.

classificação | Regressão | Previsão de série temporal
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
[Classificação DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador Linear de DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


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
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Buscar dados para executar o experimento em computação remota

Se você estiver usando computação remota para executar o experimento, a busca de dados precisará ser encapsulada em um script `get_data()` separado do Python. Esse script é executado na computação remota em que o experimento de aprendizado de máquina automatizado é executado. `get_data` elimina a necessidade de buscar os dados durante a transmissão para cada iteração. Sem `get_data`, o experimento falhará ao ser executado em computação remota.

Veja um exemplo de `get_data`:

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

No objeto `AutoMLConfig`, você especifica o parâmetro `data_script` e fornece o caminho para o arquivo de script `get_data` desta forma:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

O script `get_data` pode retornar:

Chave | Type | Mutuamente exclusivo com    | DESCRIÇÃO
---|---|---|---
X | Dataframe do Pandas ou matriz Numpy | data_train, rótulo, colunas |  Todos os recursos a serem treinados com
y | Dataframe do Pandas ou matriz Numpy |   label   | Rotular dados a serem treinados com. Para classificação, precisa ser uma matriz de inteiros.
X_valid | Dataframe do Pandas ou matriz Numpy   | data_train, rótulo | _Opcional_ Todos os recursos a serem validados com. Se não for especificado, X será dividido entre "treinar" e "validar"
y_valid |   Dataframe do Pandas ou matriz Numpy | data_train, rótulo | _Opcional_ Os dados de rótulo a serem validados com. Se não for especificado, y será dividido entre "treinar" e "validar"
sample_weight | Dataframe do Pandas ou matriz Numpy |   data_train, rótulo, colunas| _Opcional_ Um valor de peso para cada amostra. Use quando quiser atribuir pesos diferentes aos pontos de dados
sample_weight_valid | Dataframe do Pandas ou matriz Numpy | data_train, rótulo, colunas |    _Opcional_ Um valor de peso para cada amostra de validação. Se não for especificado, sample_weight será dividido entre "treinar" e "validar"
data_train |    Dataframe do Pandas |  X, y, X_valid, y_valid |    Todos os dados (recursos + rótulo) a serem treinados com
label | string  | X, y, X_valid, y_valid |  Qual coluna em data_train representa o rótulo
colunas | Matriz de cadeias de caracteres  ||  _Opcional_ Lista de permissões de colunas a serem usadas para recursos
cv_splits_indices   | Matriz de inteiros ||  _Opcional_ Lista de índices para dividir os dados para validação cruzada

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Carregar e preparar dados usando o SDK de DataPrep
Os experimentos de aprendizado de máquina automatizado são compatíveis com o carregamento de dados e transformações usando o SDK de dataprep. Usar o SDK oferece a capacidade de

>* Carregar de muitos tipos de arquivos com inferência de parâmetros de análise (codificação, separador, cabeçalhos)
>* Conversão de tipos usando inferência durante o carregamento de arquivos
>* Suporte de conexão para o MS SQL Server e o Azure Data Lake Storage
>* Adicionar coluna usando uma expressão
>* Acrescentar valores ausentes
>* Derivar colunas por exemplo
>* Filtragem
>* Transformações personalizadas de Python

Para saber mais sobre o sdk de preparação de dados, confira o artigo [Como se preparar para a modelagem](how-to-load-data.md).
Veja abaixo um exemplo de dados de carregamento usando o sdk de preparação de dados.
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

É possível especificar conjuntos de treinamento e validação por meio de get_data() ou diretamente no método `AutoMLConfig`.

## <a name="cross-validation-split-options"></a>Opções de divisão de validação cruzada

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. As pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. 

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Validação cruzada Monte Carlo (também conhecida como subamostragem aleatória repetida)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. Monte Carlo, não há suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use o conjunto de dados de validação personalizada se divisão aleatória não for aceitável, normalmente, dados de série temporal ou dados em desequilíbrio. É possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir:
*   O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
*   Um computador remoto na nuvem – [Computação gerenciada do Azure Machine Learning](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) é um serviço gerenciado que permite a capacidade de treinar modelos de machine learning em clusters de máquinas virtuais do Azure.

Confira o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de notebooks com destinos de computação locais e remotos.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) para obter uma lista completa de parâmetros.  

Alguns exemplos incluem:

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

Os três diferentes `task` valores de parâmetro de determinam a lista de algoritmos a ser aplicado.  Use os parâmetros `whitelist` ou `blacklist` para modificar ainda mais as iterações com os algoritmos disponíveis a serem incluídos ou excluídos. A lista de modelos com suporte pode ser encontrada no [SupportedAlgorithms classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

## <a name="primary-metric"></a>Métrica principal
A principal métrica; conforme mostrado nos exemplos acima determina a métrica a ser usado durante o treinamento de modelo para otimização. A principal métrica que você pode selecionar é determinada pelo tipo de tarefa que você escolher. Abaixo está uma lista de métricas disponíveis.

|classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

## <a name="data-pre-processing-and-featurization"></a>Pré-processamento e personalização de dados

Se você usar `preprocess=True`, os dados a seguir as etapas de pré-processamento serão executados automaticamente para você:
1.  Remover alta cardinalidade ou nenhum recurso de variação
    * Remova recursos sem informações úteis do treinamento e dos conjuntos de validação. Eles incluem recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).
1.  Atribuição de valor ausente
    *   Para recursos numéricos, atribua valores ausentes com a média dos valores na coluna.
    *   Para recursos categóricos, atribua valores ausentes com o valor mais frequente.
1.  Gerar recursos adicionais
    * Para recursos DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.
    * Para recursos Text: Frequência de termo com base em unigrama, bigramas e trigrama e vetorizador de contagem de palavras.
1.  Transformações e codificações
    * Os recursos numéricos com poucos valores únicos transformados em recursos categóricos.
    * Dependendo da cardinalidade dos recursos categóricos, realize a codificação de rótulo ou a codificação one-hot (hashing).

## <a name="ensemble-models"></a>Modelos de Ensemble
Aprendizado de Ensemble melhora o desempenho de previsão e resultados de aprendizado de máquina combinando vários modelos em vez de usar modelos únicos. Ao usar automatizadas aprendizado de máquina, você pode treinar modelos de ensemble usando o [algoritmo de seleção de ensemble Caruana com a inicialização de Ensemble classificado](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). A iteração de ensemble aparece como a última iteração de seu tempo de execução.

## <a name="run-experiment"></a>Executar o experimento

Envie o experimento para ser executado e para gerar um modelo. Passe o `AutoMLConfig` para o método `submit` para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez em um novo computador.  Pode levar até dez minutos antes da saída ser exibida.
>Definir `show_output` como `True` faz a saída ser mostrada no console.

## <a name="exit-criteria"></a>Critérios de saída 
Há algumas opções você pode definir para concluir seu experimento.
1. Nenhum critério - se você não definir nenhum sair parâmetros que o teste continuará até que nenhum progresso adicional é feito em sua métrica principal. 
1. Número de iterações - você definir o número de iterações para executar o experimento. Você pode opcional adicionar iteration_timeout_minutes para definir um limite de tempo em minutos por cada iteração.
1. Sair após um período de tempo - experiment_timeout_minutes usando em suas configurações, que você pode definir quanto tempo em minutos um experimento que deve continuar em execução.
1. Feche após uma pontuação foi atingida - usando experiment_exit_score, que você pode escolher para o experiement após uma pontuação com base em sua métrica principal foi atingida.

## <a name="explore-model-metrics"></a>Explorar as métricas do modelo
Será possível exibir os resultados em um widget ou embutidos se você estiver usando um notebook. Confira [Track and evaluate models](how-to-track-experiments.md#view-run-details) (Rastrear e avaliar modelos) para obter mais detalhes.


### <a name="classification-metrics"></a>Métricas de classificação
As métricas a seguir são salvas em cada iteração para uma tarefa de classificação.

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
norm_macro_recall|O Recolhimento de macro normalizado é o recolhimento de macro normalizado para que o desempenho aleatório tenha uma pontuação de 0 e o desempenho perfeito tenha uma pontuação de 1. Isso é obtido por norm_macro_recall := (recall_score_macro - R)/(1 - R), em que R é o valor esperado de recall_score_macro para previsões aleatórias (i.e., R=0,5 para classificação binária e R=(1/C) para problemas de classificação da classe C)|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro" e (recall_score_macro - R)/(1 - R), em que R é o valor esperado de recall_score_macro para previsões aleatórias (i.e., R=0,5 para classificação binária e R=(1/C) para problemas de classificação da classe C)|
precision_score_macro|A precisão é o percentual de elementos rotulados como uma determinada classe que realmente estão nessa classe. Macro é a média aritmética de precisão para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|A precisão é o percentual de elementos rotulados como uma determinada classe que realmente estão nessa classe. Micro é calculado globalmente contando o total de positivos verdadeiros e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|A precisão é o percentual de elementos rotulados como uma determinada classe que realmente estão nessa classe. Weighted é a média aritmética da precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|O recolhimento é o percentual de elementos que realmente estão em uma determinada classe que são rotulados corretamente. Macro é a média aritmética de recolhimento para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|O recolhimento é o percentual de elementos que realmente estão em uma determinada classe que são rotulados corretamente. Micro é computado globalmente contando o total de positivos verdadeiros e falsos negativos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|O recolhimento é o percentual de elementos que realmente estão em uma determinada classe que são rotulados corretamente. Weighted é a média aritmética de recolhimento para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Precisão ponderada é a precisão em que o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na classe verdadeira desse exemplo|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual à proporção dessa classe para cada elemento no destino|

### <a name="regression-and-time-series-forecasting-metrics"></a>Métricas de previsão de série de tempo e de regressão
As métricas a seguir são salvas em cada iteração para uma tarefa de regressão ou de previsão.

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

## <a name="explain-the-model"></a>Explicar o modelo

Embora as funcionalidades de aprendizado de máquina automatizado geralmente estejam disponíveis, **o recurso da capacidade de explicação do modelo ainda está em versão prévia pública.**

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

É possível visualizar o gráfico de importância do recurso em seu workspace no portal do Azure. O gráfico também é mostrado ao usar o widget do Jupyter em um notebook. Para saber mais sobre os gráficos, veja o [artigo de notebooks de Serviço do Azure Machine Learning de exemplo.](samples-notebooks.md)

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![gráfico de importância do recurso](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de regressão com aprendizado de máquina automatizada](tutorial-auto-train-models.md) ou [como treinar usando automatizada de aprendizado de máquina em um recurso remoto](how-to-auto-train-remote.md).
