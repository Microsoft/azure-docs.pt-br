---
title: Treinar automaticamente um modelo de previsão de série temporal
titleSuffix: Azure Machine Learning
description: Saiba como usar o Azure Machine Learning para treinar um modelo de regressão de previsão de série temporal usando o machine learning automatizado.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 6e686c7b22eb834a096cdd7a67beb6d8d291ef20
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392316"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Treinar automaticamente um modelo de previsão de série temporal


Neste artigo, você aprenderá a configurar e treinar um modelo de regressão de previsão de série temporal usando o Machine Learning automatizado, AutoML, no [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Para fazer isso, você deve: 

> [!div class="checklist"]
> * Preparar dados para a modelagem de série temporal.
> * Configure parâmetros de série temporal específicos em um [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objeto.
> * Executar previsões com dados de série temporal.

Para uma experiência com pouco código, veja o [Tutorial: Prever a demanda com o machine learning automatizado](tutorial-automated-ml-forecast.md) para conhecer um exemplo de previsão de série temporal usando o machine learning automatizado no [Azure Machine Learning Studio](https://ml.azure.com/).

Ao contrário dos métodos de série temporal clássicas, nos valores de série de tempo anteriores de ML são "dinamizados" para se tornarem dimensões adicionais para o regressor junto com outros pregnósticos. Essa abordagem incorpora várias variáveis contextuais e sua relação entre si durante o treinamento. Como vários fatores podem influenciar uma previsão, esse método se alinha bem com os cenários reais de previsão. Por exemplo, ao prever vendas, interações de tendências históricas, taxa de câmbio e preço, todos orientam o resultado de vendas. 

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa, 

* Um Workspace do Azure Machine Learning. Para criar o workspace, consulte [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

* Este artigo pressupõe alguma familiaridade com a configuração de um experimento de aprendizado de máquina automatizado. Siga o [tutorial](tutorial-auto-train-models.md) ou [instruções](how-to-configure-auto-train.md) para ver os principais padrões de design do experimento do Machine Learning automatizado.

## <a name="preparing-data"></a>Preparando dados

A diferença mais importante entre um tipo de tarefa de regressão de previsão e o tipo de tarefa de regressão dentro de AutoML está incluindo um recurso em seus dados que representa uma série temporal válida. Uma série temporal regular tem uma frequência bem definida e consistente e um valor em cada ponto de amostra em um período de tempo contínuo. 

Considere o seguinte instantâneo de um arquivo `sample.csv`.
Esse conjunto de dados é de dados de vendas diários para uma empresa que tem duas lojas diferentes, A e B. 

Além disso, há recursos para

 *  `week_of_year`: permite que o modelo detecte sazonalidade semanalmente.
* `day_datetime`: representa uma série temporal limpa com frequência diária.
* `sales_quantity`: a coluna de destino para executar previsões. 

```output
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
```


Leia os dados em um dataframe do Pandas e use a função `to_datetime` para garantir que a série temporal seja um tipo `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

Nesse caso, os dados já estão classificados em ordem crescente pelo campo de tempo `day_datetime`. No entanto, ao configurar um experimento, verifique se a coluna de tempo desejada está classificada em ordem crescente para criar uma série temporal válida. 

O código a seguir, 
* Pressupõe que os dados contêm 1.000 registros e faz com que uma divisão determinística nos dados Crie conjuntos de dados de treinamento e teste. 
* Identifica a coluna de rótulo como `sales_quantity` .
* Separa o campo de rótulo de `test_data` para formar o `test_target` conjunto.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> Ao treinar um modelo para prever valores futuros, verifique se todos os recursos usados no treinamento podem ser usados ao executar previsões para o horizonte pretendido. <br> <br>Por exemplo, ao criar uma previsão de demanda, incluir um recurso para o preço de ações atual poderia aumentar imensamente a precisão do treinamento. No entanto, se você pretende fazer uma previsão com horizonte longo, talvez não seja possível prever com precisão valores de ações futuros correspondentes a pontos de série temporal futuros, e a precisão do modelo poderá ser prejudicada.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Dados de treinamento e validação

Você pode especificar conjuntos de treinamento e de validação separados diretamente no `AutoMLConfig` objeto.   Saiba mais sobre o [AutoMLConfig](#configure-experiment).

Para a previsão de séries temporais, somente a **ROCV (validação cruzada de origem)** é usada para validação por padrão. Passe os dados de treinamento e validação juntos e defina o número de dobras de validação cruzada com o `n_cross_validations` parâmetro no seu `AutoMLConfig` . O ROCV divide a série em dados de treinamento e de validação usando um ponto de tempo de origem. Deslizar a origem no tempo gera as dobras de validação cruzada. Essa estratégia preserva a integridade dos dados da série temporal e elimina o risco de vazamento de dados

![validação cruzada de origem sem interrupção](./media/how-to-auto-train-forecast/ROCV.svg)

Você também pode trazer seus próprios dados de validação, saiba mais em [Configurar divisões de dados e validação cruzada no AutoML](how-to-configure-cross-validation-data-splits.md#provide-validation-data).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

Saiba mais sobre como o AutoML aplica a validação cruzada para [evitar modelos de sobreajuste](concept-manage-ml-pitfalls.md#prevent-over-fitting).

## <a name="configure-experiment"></a>Configurar o experimento

O objeto [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) define as configurações e os dados necessários para uma tarefa de machine learning automatizado. A configuração de um modelo de previsão é semelhante à configuração de um modelo de regressão padrão, mas determinados modelos, opções de configuração e etapas de personalização existem especificamente para dados de série temporal. 

### <a name="supported-models"></a>Modelos com suporte
O aprendizado de máquina automatizado tenta automaticamente modelos e algoritmos diferentes como parte do processo de criação e ajuste do modelo. Como usuário, não há necessidade de especificar o algoritmo. Para prever experimentos, os modelos de série temporal nativa e de aprendizado profundo fazem parte do sistema de recomendação. A tabela a seguir resume esse subconjunto de modelos. 

>[!Tip]
> Os modelos de regressão tradicionais também são testados como parte do sistema de recomendação para prever experimentos. Consulte a [tabela de modelos com suporte](how-to-configure-auto-train.md#supported-models) para obter a lista completa de modelos. 

Modelos| Descrição | Benefícios
----|----|---
Prophet (versão prévia)|O Prophet funciona melhor com séries temporais com efeitos sazonais fortes e várias estações de dados históricos. Para aproveitar esse modelo, instale-o localmente usando o `pip install fbprophet` . | Precisão e rapidez, robusto a exceções, dados ausentes e alterações significativas na sua série temporal.
Auto-ARIMA (versão prévia)|A média de movimentação integrada de regressão automática (ARIMA) funciona melhor, quando os dados são estáticos. Isso significa que suas propriedades estatísticas, como média e variância, são constantes em todo o conjunto. Por exemplo, se você virar uma moeda, a probabilidade de você obter o cabeçote será de 50%, independentemente se você virar hoje, amanhã ou ano seguinte.| Ótimo para série monovariável, já que os valores anteriores são usados para prever os valores futuros.
ForecastTCN (versão prévia)| O ForecastTCN é um modelo de rede neural projetado para lidar com as tarefas de previsão mais exigentes, capturando tendências locais e globais não lineares nos seus dados, além de relações entre as séries temporais.|Pode aproveitar tendências complexas nos seus dados e ser rapidamente dimensionado para os maiores de conjuntos de dados.

### <a name="configuration-settings"></a>Definições de configuração

Como em um problema de regressão, você define parâmetros de treinamento padrão, como tipo de tarefa, número de iterações, dados de treinamento e número de validações cruzadas. Para tarefas de previsão, há parâmetros adicionais que afetam o experimento e precisam ser definidos. 

A tabela a seguir resume esses parâmetros adicionais. Consulte a [documentação de referência da classe ForecastingParameter](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) para obter padrões de design de sintaxe.

| Nome&nbsp;do parâmetro | Descrição | Obrigatório |
|-------|-------|-------|
|`time_column_name`|Usado para especificar a coluna datetime nos dados de entrada usados para criar a série temporal e inferir sua frequência.|✓|
|`forecast_horizon`|Define quantos períodos encaminhar você gostaria de prever. O horizonte está em unidades da frequência da série temporal. As unidades são baseadas no intervalo de tempo dos seus dados de treinamento (por exemplo, mensalmente, semanalmente) em que o forecaster deve prever.|✓|
|`enable_dnn`|[Habilitar previsão de DNNs]().||
|`time_series_id_column_names`|Os nomes de coluna usados para identificar exclusivamente a série temporal em dados que têm várias linhas com o mesmo carimbo de data/hora. Se os identificadores de série temporal não estiverem definidos, o conjunto de dados será considerado uma série temporal. Para saber mais sobre série temporal única, consulte [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).||
|`freq`| A frequência do conjunto de tempo da série temporal. Esse parâmetro representa o período com o qual os eventos devem ocorrer, como diário, semanal, anual, etc. A frequência deve ser um [alias de deslocamento de pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects).||
|`target_lags`|Número de linhas para retardar os valores de destino com base na frequência dos dados. O retardo é representado como uma lista ou um único inteiro. O retardo deve ser usado quando a relação entre as variáveis independentes e a variável dependente não corresponde ou se correlaciona por padrão. ||
|`feature_lags`| Os recursos a serem defasados serão automaticamente decididos pelo ML automatizado quando `target_lags` estiverem definidos e `feature_lags` definidos como `auto` . Habilitar o recurso retardo pode ajudar a melhorar a precisão. O recurso retardo está desabilitado por padrão. ||
|`target_rolling_window_size`|*n* períodos históricos para usar os valores previstos gerado, <= o tamanho do conjunto de treinamento. Se omitido, *n* será o tamanho total do conjunto de treinamento. Especifique esse parâmetro quando desejar considerar apenas certa quantidade de histórico no treinamento do modelo. Saiba mais sobre a [agregação de janela de destino](#target-rolling-window-aggregation).||
|`short_series_handling_config`| Habilita o tratamento de série temporal curta para evitar a falha durante o treinamento devido a dados insuficientes. A manipulação de série curta é definida como `auto` por padrão. Saiba mais sobre a [manipulação de série curta](#short-series-handling).|


O código a seguir, 
* Aproveita a [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) classe para definir os parâmetros de previsão para o treinamento do experimento
* Define o `time_column_name` para o `day_datetime` campo no conjunto de dados. 
* Define o `time_series_id_column_names` parâmetro para `"store"` . Isso garante que **dois grupos de série temporal separados** sejam criados para os dados; uma para A loja A e B.
* Define o `forecast_horizon` como 50 para prever o conjunto de teste inteiro. 
* Define uma janela de previsão como 10 períodos com `target_rolling_window_size`
* Especifica um retardo único nos valores de destino para dois períodos à frente com o `target_lags` parâmetro. 
* Define `target_lags` para a configuração "automática" recomendada, que detectará automaticamente esse valor para você.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

`forecasting_parameters`Em seguida, eles são passados para o `AutoMLConfig` objeto padrão junto com o `forecasting` tipo de tarefa, métrica primária, critérios de saída e dados de treinamento. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

A quantidade de dados necessária para treinar com êxito um modelo de previsão com ML automatizado é influenciada `forecast_horizon` pelos `n_cross_validations` valores, e `target_lags` ou `target_rolling_window_size` especificados quando você configura o `AutoMLConfig` . 

A fórmula a seguir calcula a quantidade de dados históricos que seriam necessários para construir recursos de série temporal.

Dados históricos mínimos necessários: (2x `forecast_horizon` ) + # `n_cross_validations` + Max (Max ( `target_lags` ), `target_rolling_window_size` )

Uma exceção de erro será gerada para qualquer série no conjunto de dados que não atenda à quantidade necessária de dado históricos para as configurações relevantes especificadas. 

### <a name="featurization-steps"></a>Etapas de personalização

Em cada experimento automatizado de aprendizado de máquina, técnicas de dimensionamento automático e de normalização são aplicadas aos seus dados por padrão. Essas técnicas são tipos de **personalização** que ajudam *certos* algoritmos que são sensíveis a recursos em escalas diferentes. Saiba mais sobre as etapas de personalização padrão no [personalização no AutoML](how-to-configure-auto-features.md#automatic-featurization)

No entanto, as etapas a seguir são executadas somente para `forecasting` tipos de tarefa:

* Detectar a frequência de amostragem da série temporal (por exemplo, por hora, diária, semanal) e criar registros novos para pontos de tempo ausentes para tornar a série contínua.
* Imputar valores ausentes no destino (via preenchimento à frente) e colunas de recurso (usando valor mediano de coluna)
* Criar recursos com base em identificadores de série temporal para habilitar efeitos fixos em diferentes séries
* Criar recursos baseados em tempo para auxiliar no aprendizado de padrões sazonais
* Codificar variáveis categóricas em quantidades numéricas

Para obter um resumo dos recursos que são criados como resultado dessas etapas, consulte [transparência de personalização](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> As etapas de definição de recursos de machine learning automatizado (normalização de recursos, manipulação de dados ausentes, conversão de texto em números, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de definição de recursos aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

#### <a name="customize-featurization"></a>Personalizar o personalização

Você também tem a opção de personalizar suas configurações de personalização para garantir que os dados e recursos usados para treinar seu modelo de ML resultem em previsões relevantes. 

As personalizações com suporte para `forecasting` as tarefas incluem:

|Personalização|Definição|
|--|--|
|**Atualização de finalidade de coluna**|Substituir o tipo de recurso detectado automaticamente para a coluna especificada.|
|**Atualização de parâmetro do transformador** |Atualize os parâmetros para o transformador especificado. Atualmente dá suporte a *imputer* (fill_value e mediana).|
|**Remover colunas** |Especifica que as colunas a serem descartadas são destacados.|

Para personalizar o featurizations com o SDK, especifique `"featurization": FeaturizationConfig` em seu `AutoMLConfig` objeto. Saiba mais sobre o [featurizations personalizado](how-to-configure-auto-features.md#customize-featurization).

>[!NOTE]
> A funcionalidade **remover colunas** foi preterida a partir da versão 1,19 do SDK. Remova as colunas do conjunto de dados como parte da limpeza, antes de consumi-la em seu experimento de ML automatizado. 

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Se você estiver usando o Azure Machine Learning Studio para seu experimento, consulte [como personalizar o personalização no estúdio](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

## <a name="optional-configurations"></a>Configurações opcionais

Configurações opcionais adicionais estão disponíveis para tarefas de previsão, como habilitar o aprendizado profundo e especificar uma agregação de janela de desativação de destino. 

### <a name="enable-deep-learning"></a>Habilitar o aprendizado profundo

> [!NOTE]
> O suporte do DNN para previsão no Machine Learning automatizado está em versão **prévia** e não tem suporte para execuções locais.

Você também pode aproveitar o aprendizado profundo com redes neurais profundas, DNNs, para melhorar as pontuações de seu modelo. O aprendizado profundo do machine learning automatizado possibilita a previsão de dados de série temporal monovariável e multivariada.

Os modelos de aprendizado profundo têm três recursos intrínsecos:
1. Eles podem aprender com mapeamentos arbitrários de entradas para saídas
1. Eles tem suporte a várias entradas e saídas
1. Eles podem extrair automaticamente padrões em dados de entrada que se estendem por longas sequências. 

Para habilitar o aprendizado profundo, defina o `enable_dnn=True` no `AutoMLConfig` objeto.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> Quando você habilita o DNN para experimentos criados com o SDK, [as melhores explicações de modelo](how-to-machine-learning-interpretability-automl.md) são desabilitadas.

Para habilitar o DNN para um experimento do AutoML criado no Azure Machine Learning Studio, consulte as [configurações de tipo de tarefa no instruções do estúdio](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

Veja o [notebook de previsão de produção de bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) para conhecer um exemplo de código detalhado que aproveita DNNs.

### <a name="target-rolling-window-aggregation"></a>Agregações de janelas sem interrupção como destino
Frequentemente, a melhor informação que um forecaster pode ter é um valor recente no destino.  As agregações de janela de destino permitem que você adicione uma agregação sem interrupção de valores de dados como recursos. Gerar e usar esses recursos adicionais como dados contextuais extras ajuda a tornar o modelo de treinamento mais preciso.

Por exemplo, digamos que você deseja prever a demanda de energia. Talvez você queira adicionar um recurso de janela sem interrupção de três dias para considerar as alterações térmicas de espaços aquecidos. Neste exemplo, crie essa janela definindo `target_rolling_window_size= 3` no `AutoMLConfig` Construtor. 

A tabela mostra a engenharia de recursos resultante que ocorre quando a agregação de janela é aplicada. Colunas para **mínimo, máximo** e **soma** são geradas em uma janela deslizante de três com base nas configurações definidas. Cada linha tem um novo recurso calculado, no caso do carimbo de data/hora de 8 de setembro de 2017 4:10:00 os valores máximo, mínimo e soma são calculados usando os **valores de demanda** de 8 de setembro de 2017 1:10:00-3:10:00. Essa janela de três se desloca para preencher os dados das linhas restantes.

![janela de rolagem de destino](./media/how-to-auto-train-forecast/target-roll.svg)

Veja um exemplo de código Python que aproveita o [recurso de agregação de janela sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="short-series-handling"></a>Manipulação de série curta

O ML automatizado considera uma série temporal uma **pequena seqüência** se não houver pontos de dados suficientes para conduzir as fases de treinamento e validação do desenvolvimento de modelo. O número de pontos de dados varia para cada experimento e depende do max_horizon, do número de divisões de validação cruzada e do comprimento do modelo lookback, que é o máximo do histórico necessário para construir os recursos da série temporal. Para obter o cálculo exato, consulte a [documentação de referência do short_series_handling_configuration](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py#short-series-handling-configuration).

O ML automatizado oferece manipulação de série curta por padrão com o `short_series_handling_configuration` parâmetro no `ForecastingParameters` objeto. 

Para habilitar o tratamento de séries curtas, o `freq` parâmetro também deve ser definido. Para definir uma frequência por hora, definiremos `freq='H'` . Veja as opções de cadeia de caracteres de frequência [aqui](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects). Para alterar o comportamento padrão, `short_series_handling_configuration = 'auto'` atualize o `short_series_handling_configuration` parâmetro em seu `ForecastingParameter` objeto.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
A tabela a seguir resume as configurações disponíveis para o `short_series_handling_config` .
 
|Setting|Descrição
|---|---
|`auto`| Este é o comportamento padrão para manipulação de série curta <li> *Se todas as séries forem curtas*, preencha os dados. <br> <li> *Se nem todas as séries forem curtas*, descarte a série curta. 
|`pad`| Se `short_series_handling_config = pad` , o ml automatizado adiciona valores aleatórios a cada série curta encontrada. O seguinte lista os tipos de coluna e com que eles são preenchidos: <li>Colunas de objeto com NaNs <li> Colunas numéricas com 0 <li> Colunas boolianas/lógicas com false <li> A coluna de destino é preenchida com valores aleatórios com média de zero e desvio padrão de 1. 
|`drop`| Se `short_series_handling_config = drop` , em seguida, o ml automatizado descartará a série curta e não será usado para treinamento ou previsão. As previsões para essas séries retornam NaN.
|`None`| Nenhuma série é preenchida ou descartada

>[!WARNING]
>O preenchimento pode afetar a precisão do modelo resultante, já que estamos introduzindo dados artificiais apenas para obter treinamentos anteriores sem falhas. <br> <br> Se muitas das séries forem curtas, você também poderá ver algum impacto nos resultados da explicação

## <a name="run-the-experiment"></a>Execute o experimento 

Quando o objeto estiver `AutoMLConfig` pronto, você poderá enviar o experimento. Após a conclusão do modelo, recupere a melhor iteração de execução.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>Previsões com o melhor modelo

Use a melhor iteração de modelo para prever valores para o conjunto de dados de teste.

A `forecast()` função permite que as especificações de quando as previsões devem ser iniciadas, ao contrário do `predict()` , que normalmente é usado para tarefas de classificação e regressão.

No exemplo a seguir, você primeiro substitui todos os valores em `y_pred` por `NaN`. Nesse caso, a origem da previsão estará no final dos dados de treinamento. No entanto, se você substituiu apenas a segunda metade de `y_pred` por `NaN`, a função manterá os valores numéricos na primeira metade sem modificações, mas preverá os valores de `NaN` na segunda metade. A função retorna os valores previstos e os recursos alinhados.

Você também pode usar o parâmetro `forecast_destination` na função `forecast()` para prever valores até uma data especificada.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcule o erro de quadrado da média de raiz (RMSE) entre os `actual_labels` valores reais e os valores previstos em `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Agora que a precisão geral do modelo foi determinada, a próxima etapa mais realista é usar o modelo para prever valores futuros desconhecidos. 

Forneça um conjunto de dados no mesmo formato do conjunto de teste `test_data`, mas com datetimes futuros, e o conjunto de previsão resultante será os valores previstos para cada etapa da série temporal. Suponha que os últimos registros de série temporal no conjunto de dados eram de 31/12/2018. Para prever a demanda do dia seguinte (ou quantos períodos forem necessários prever, até `forecast_horizon`), crie um único registro de série temporal para cada loja para 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Repita as etapas necessárias para carregar esses dados futuros em um dataframe e, em seguida, execute `best_run.predict(test_data)` para prever valores futuros.

> [!NOTE]
> As previsões em amostra não têm suporte para previsão com ML automatizado quando `target_lags` e/ou `target_rolling_window_size` estão habilitados.


## <a name="example-notebooks"></a>Blocos de anotações de exemplo
Consulte os [notebooks de amostra de previsão](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para ver exemplos de código detalhados de configuração de previsão avançada, incluindo:

* [detecção de feriados e engenharia de recursos](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validação cruzada de origem sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [retardos configuráveis](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [recursos de agregação de janela sem interrupção](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).
* Saiba mais sobre a [interpretação: explicações de modelo no Machine Learning automatizado (visualização)](how-to-machine-learning-interpretability-automl.md). 
* Saiba como treinar vários modelos com o AutoML no [acelerador de solução de vários modelos](https://aka.ms/many-models).
* Siga o [tutorial](tutorial-auto-train-models.md) para obter um exemplo de ponta a ponta para criar experimentos com o aprendizado de máquina automatizado.
