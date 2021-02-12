---
title: Divisões de dados e validação cruzada no Machine Learning automatizado
titleSuffix: Azure Machine Learning
description: Saiba como configurar divisões de conjunto de informações e validação cruzada para experiências automatizadas de aprendizado de máquina
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: a781900534156e455c125dffe3b1334820fdf4d5
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599070"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Configurar divisões de dados e validação cruzada no machine learning automatizado

Neste artigo, você aprende as diferentes opções para configurar dados de treinamento e dados de validação divididos juntamente com configurações de validação cruzada para aprendizado de máquina automatizado, ML automatizado, experimentos.

Em Azure Machine Learning, quando você usa o ML automatizado para criar vários modelos de ML, cada execução filho precisa validar o modelo relacionado calculando as métricas de qualidade para esse modelo, como precisão ou AUC ponderado. Essas métricas são calculadas comparando as previsões feitas com cada modelo com rótulos reais das observações anteriores nos dados de validação. [Saiba mais sobre como as métricas são calculadas com base no tipo de validação](#metric-calculation-for-cross-validation-in-machine-learning). 

As experiências de ML automatizadas executam automaticamente a validação do modelo. As seções a seguir descrevem como você pode personalizar ainda mais as configurações de validação com o [SDK Azure Machine Learning Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Para uma experiência de baixo código ou sem código, confira [criar experiências automatizadas de aprendizado de máquina no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> Atualmente, o estúdio dá suporte a divisões de dados de treinamento e validação, bem como opções de validação cruzada, mas não oferece suporte à especificação de arquivos de dados individuais para seu conjunto de validação. 

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa,

* Um Workspace do Azure Machine Learning. Para criar o workspace, consulte [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

* Familiaridade com a configuração de um experimento de aprendizado de máquina automatizado com o SDK do Azure Machine Learning. Siga o [tutorial](tutorial-auto-train-models.md) ou [instruções](how-to-configure-auto-train.md) para ver os padrões fundamentais de design de teste de Machine Learning automatizado.

* Noções básicas sobre divisão/validação de dados de treinamento e validação cruzada como conceitos de aprendizado de máquina. Para obter uma explicação de alto nível,

    * [Sobre dados de treinamento, validação e teste no aprendizado de máquina](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Entender a validação cruzada no aprendizado de máquina](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Divisões de dados padrão e validação cruzada no aprendizado de máquina

Use o objeto [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) para definir suas configurações de teste e treinamento. No trecho de código a seguir, observe que apenas os parâmetros necessários são definidos, que são os parâmetros `n_cross_validation` para `validation_ data` ou **não** estão incluídos.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Se você não especificar explicitamente um `validation_data` `n_cross_validation` parâmetro ou, o ml automatizado aplicará técnicas padrão dependendo do número de linhas fornecidas no único conjunto de um `training_data` :

|&nbsp;Tamanho dos dados de treinamento &nbsp;| Técnica de validação |
|---|-----|
|**Mais &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A divisão de dados de treinamento/validação é aplicada. O padrão é levar 10% do conjunto de dados de treinamento inicial como o conjunto de validação. Por sua vez, esse conjunto de validação é usado para cálculo de métricas.
|**Menos &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A abordagem de validação cruzada é aplicada. O número padrão de dobras depende do número de linhas. <br> **Se o conjunto de registros for menor que 1.000 linhas**, serão usadas 10 dobras. <br> **Se as linhas estiverem entre 1.000 e 20.000**, serão usadas três dobras.

## <a name="provide-validation-data"></a>Fornecer dados de validação

Nesse caso, você pode começar com um único arquivo de dados e dividi-lo em dados de treinamento e conjuntos de dados de validação ou pode fornecer um arquivo de dados separado para o conjunto de validação. De qualquer forma, o `validation_data` parâmetro em seu `AutoMLConfig` objeto atribui os dados a serem usados como seu conjunto de validação. Esse parâmetro só aceita conjuntos de dados na forma de um [conjunto](how-to-create-register-datasets.md) de dados Azure Machine Learning ou pandas dataframe.   

O exemplo de código a seguir define explicitamente qual parte dos dados fornecidos no `dataset` usar para treinamento e validação.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Fornecer tamanho do conjunto de validação

Nesse caso, apenas um único conjunto de um é fornecido para o experimento. Ou seja, o `validation_data` parâmetro **não** é especificado e o conjunto de e fornecido é atribuído ao  `training_data` parâmetro.  Em seu `AutoMLConfig` objeto, você pode definir o `validation_size` parâmetro para manter uma parte dos dados de treinamento para validação. Isso significa que o conjunto de validação será dividido por AutoML do inicial `training_data` fornecido. Esse valor deve estar entre 0,0 e 1,0 não inclusivo (por exemplo, 0,2 significa que 20% dos dados são mantidos para dados de validação).

Consulte o exemplo de código a seguir:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>Definir o número de validações cruzadas

Para executar a validação cruzada, inclua o `n_cross_validations` parâmetro e defina-o como um valor. Esse parâmetro define quantas validações cruzadas executar, com base no mesmo número de dobras.

No código a seguir, são definidas cinco dobras para validação cruzada. Portanto, cinco treinamentos diferentes, cada um treinando usando 4/5 dos dados e cada validação usando 1/5 dos dados com uma dobra de controle diferente a cada vez.

Como resultado, as métricas são calculadas com a média das cinco métricas de validação.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Especificar partições de dados de validação cruzada personalizadas

Você também pode fornecer suas próprias dobras de dados de validação cruzada (CV). Isso é considerado um cenário mais avançado porque você está especificando quais colunas devem ser divididas e usadas para validação.  Inclua colunas de divisão de CV personalizadas em seus dados de treinamento e especifique quais colunas preenchendo os nomes de coluna no `cv_split_column_names` parâmetro. Cada coluna representa uma divisão de validação cruzada e é preenchida com valores inteiros 1 ou 0--em que 1 indica que a linha deve ser usada para treinamento e 0 indica que a linha deve ser usada para validação.

O trecho de código a seguir contém dados de marketing bancário com duas colunas de divisão de CV ' CV1 ' e ' CV2 '.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Para usar `cv_split_column_names` `training_data` o com `label_column_name` o e o, atualize seu Azure Machine Learning SDK do Python versão 1.6.0 ou posterior. Para versões anteriores do SDK, consulte usando `cv_splits_indices` , mas observe que ela é usada somente com `X` e `y` entrada de conjunto de dados. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Cálculo de métrica para validação cruzada no aprendizado de máquina

Quando a validação cruzada k-fold ou Monte Carlo é usada, as métricas são computadas em cada dobra de validação e, em seguida, agregadas. A operação de agregação é uma média para métricas escalares e uma soma para gráficos. As métricas computadas durante a validação cruzada são baseadas em todas as dobras e, portanto, em todas as amostras do conjunto de treinamento. [Saiba mais sobre métricas no Machine Learning automatizado](how-to-understand-automated-ml.md).

Quando um conjunto de validação personalizado ou um conjunto de validação selecionado automaticamente é usado, as métricas de avaliação do modelo são computadas somente daquele conjunto de validação, não os dados de treinamento.

## <a name="next-steps"></a>Próximas etapas

* [Evitar dados desequilibrados e sobreajuste](concept-manage-ml-pitfalls.md).
* [Tutorial: usar o Machine Learning automatizado para prever as tarifas de táxi – seção dividir dados](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Como [treinar automaticamente um modelo de previsão de série temporal](how-to-auto-train-forecast.md).
