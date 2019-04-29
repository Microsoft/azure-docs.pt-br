---
title: Autotreinar um modelo de previsão de série temporal
titleSuffix: Azure Machine Learning service
description: Saiba como usar o serviço Azure Machine Learning para treinar um modelo de regressão previsão usando automatizada de aprendizado de máquina em série de tempo.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: c4f94dd2730dd302951b4476a292b006041b7ee8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820029"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Autotreinar um modelo de previsão de série temporal

Neste artigo, você aprenderá a treinar um modelo de regressão previsão de série temporal usando automatizado de machine learning no serviço de Azure Machine Learning. A configuração de um modelo de previsão é semelhante à configuração de um modelo de regressão padrão usando o aprendizado de máquina automatizados, mas algumas etapas de configuração Opções e o pré-processamento existem para trabalhar com dados de série temporal. Os exemplos a seguir mostram como para:

* Preparar dados para modelagem de série temporal
* Configurar parâmetros de série temporal específicos em uma [ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objeto
* Executar previsões com dados de série temporal

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace de serviço do Azure Machine Learning. Para criar o espaço de trabalho, consulte [criar um espaço de trabalho do serviço de Azure Machine Learning](setup-create-workspace.md).
* Este artigo pressupõe familiaridade básica com a configuração de uma experimento de aprendizado de máquina automatizada. Siga as [tutorial](tutorial-auto-train-models.md) ou [instruções](how-to-configure-auto-train.md) para ver a padrões de design do experimento de aprendizado de máquina automatizada básico.

## <a name="preparing-data"></a>Preparando dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e regressão tipo de tarefa dentro de aprendizado de máquina automatizados está incluindo um recurso em seus dados que representa uma série de tempo válido. Uma série de tempo regular tem uma frequência consistente e bem definida e tem um valor em cada ponto de amostra em um período de tempo contínuo. Considere o seguinte instantâneo de um arquivo `sample.csv`.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Esse conjunto de dados é um exemplo simples de dados de vendas diárias para uma empresa que tem dois repositórios diferentes, A e B. Além disso, há um recurso para `week_of_year` que permitirá que o modelo detectar periodicidade semanal. O campo `day_datetime` representa uma série de tempo limpo com frequência diária e o campo `sales_quantity` é a coluna de destino para a execução de previsões. Leia os dados em um dataframe Pandas, em seguida, usar o `to_datetime` função para garantir que a série temporal é um `datetime` tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Nesse caso, os dados já estão classificados em ordem crescente pelo campo time `day_datetime`. No entanto, ao configurar um experimento, certifique-se que a coluna de período de tempo desejado é classificada em ordem crescente para criar uma série de tempo válido. Suponha que os dados contém 1.000 registros e fazer uma divisão determinística nos dados de treinamento de criar e testar conjuntos de dados. Em seguida, separar o campo de destino `sales_quantity` para criar o teste e treinar previsão conjuntos.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Ao treinar um modelo para prever valores futuros, verifique se todos os recursos usados no treinamento podem ser usados durante a execução de previsões para o horizonte pretendido. Por exemplo, ao criar uma previsão de demanda, incluindo um recurso para o preço da ação atual poderia massivamente aumentar a precisão de treinamento. No entanto, se você pretende com um longo horizonte de previsão, pode não ser capaz de prever com precisão valores futuros de estoque correspondente aos pontos de série temporal futuras e precisão do modelo pode ser afetado.

## <a name="configure-experiment"></a>Configurar o experimento

Para tarefas de previsão, aprendizado de máquina automatizado usa as etapas de pré-processamento e a estimativa que são específicas para dados de série temporal. As seguintes etapas de pré-processamento serão executadas:

* Detecte a frequência (por exemplo, por hora, diariamente, semanalmente) de exemplo de série temporal e cria novos registros para a ausência de pontos de tempo para tornar a série contínua.
* Imputar valores ausentes nas colunas de recurso (usando valores de coluna mediano) e destino (por meio do preenchimento de encaminhamento)
* Criar com base no intervalo de recursos para permitir que os efeitos fixos entre séries diferentes
* Criar com base no tempo de recursos para ajudá-lo no aprendizado de padrões sazonais
* Codificar variáveis categóricas quantidades numéricas

O `AutoMLConfig` objeto define as configurações e os dados necessários para que uma tarefa de aprendizado de máquina automatizado. Semelhante a um problema de regressão, você define parâmetros de treinamento padrão, como o tipo de tarefa, o número de iterações, dados, de treinamento e o número de validações cruzada. Para tarefas de previsão, há parâmetros adicionais que devem ser definidos que afetam o experimento. A tabela a seguir explica cada parâmetro e seu uso.

| Param | DESCRIÇÃO | Obrigatório |
|-------|-------|-------|
|`time_column_name`|Usado para especificar a coluna de data e hora nos dados de entrada usados para criar a série temporal e inferindo sua frequência.|✓|
|`grain_column_names`|Nomes de definição de grupos de séries individuais nos dados de entrada. Se o detalhamento não estiver definido, o conjunto de dados é considerado uma série de tempo.||
|`max_horizon`|Máximo desejado horizonte de previsão em unidades de frequência de série temporal.|✓|
|`target_lags`|*n* valores antes do treinamento do modelo de destino de períodos de latência de encaminhamento.||
|`target_rolling_window_size`|*n* períodos históricos a serem usados para gerar valores previstos, < = tamanho do conjunto de treinamento. Se omitido, *n* é o treinamento completo Definir tamanho.||

Crie as configurações de série temporal como um objeto de dicionário. Defina as `time_column_name` para o `day_datetime` campo no conjunto de dados. Definir a `grain_column_names` parâmetro para garantir que **dois separam grupos de série temporal** são criados para os dados, uma para a loja A e B. por fim, defina o `max_horizon` a 50 para prever para o teste inteiro definida. Configurar uma janela de previsão para 10 períodos com `target_rolling_window_size`e o destino 2 períodos em frente com os valores de latência de `target_lags` parâmetro.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Agora, crie um padrão `AutoMLConfig` do objeto, especificando o `forecasting` tipo de tarefa e enviar o teste. Após o modelo, recupere a iteração de execução melhor.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> Para obter o procedimento de (VC) de validação cruzada, dados de série temporal podem violar as suposições básicas de estatísticas da estratégia de validação cruzada K vezes canônica, para que o aprendizado de máquina automatizado implementa um procedimento de validação de origem sem interrupção para criar dobras de validação cruzada para dados de série temporal. Para usar este procedimento, especifique o `n_cross_validations` parâmetro no `AutoMLConfig` objeto. Você pode ignorar a validação e o uso de conjuntos de sua própria validação com o `X_valid` e `y_valid` parâmetros.

## <a name="forecasting-with-best-model"></a>Com o melhor modelo de previsão

Use a melhor iteração do modelo para prever valores para o conjunto de dados de teste.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Calcular RMSE (erro de quadrado da média de raiz) entre o `y_test` valores reais e os valores previstos no `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Agora que o geral foi determinada precisão do modelo, a próxima etapa mais realista é usar o modelo para prever valores futuros desconhecidos. Basta fornecer um conjunto de dados no mesmo formato que o conjunto de teste `X_test` mas com datetimes futuras e a previsão resultante conjunto é dos valores previstos para cada etapa de série temporal. Suponha que os últimos registros de série temporal no conjunto de dados eram de 12/31/2018. Para prever a demanda para o próximo dia (ou períodos quantos forem necessários para fazer uma previsão, < = `max_horizon`), crie um único registro de série de tempo para cada repositório para 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Repita as etapas necessárias para carregar esses dados futuros para um dataframe e, em seguida, executar `best_run.predict(X_test)` para prever valores futuros.

> [!NOTE]
> Valores não podem ser previstos para o número de períodos de maior que o `max_horizon`. O modelo deve ser treinado novamente com um limite maior para prever valores futuros, além de horizonte atual.

## <a name="next-steps"></a>Próximas etapas

* Siga as [tutorial](tutorial-auto-train-models.md) para aprender a criar experiências com automatizados do aprendizado de máquina.
* Modo de exibição de [SDK de aprendizado de máquina do Azure para Python](https://aka.ms/aml-sdk) documentação de referência.
