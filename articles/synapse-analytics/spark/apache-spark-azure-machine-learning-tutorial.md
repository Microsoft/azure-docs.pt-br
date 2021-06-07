---
title: 'Tutorial: Treinar um modelo em Python com o machine learning automatizado'
description: Tutorial sobre como treinar um modelo de machine learning em Python usando o Apache Spark e o machine learning automatizado.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 89309cfe427183d594a5cc2f76332ae150d4f803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102498669"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Tutorial: Treinar um modelo em Python com o machine learning automatizado

O Azure Machine Learning é um ambiente baseado em nuvem que permite treinar, implantar, automatizar, gerenciar e acompanhar modelos de machine learning. 

Neste tutorial, você usará o [machine learning automatizado](../../machine-learning/concept-automated-ml.md) no Azure Machine Learning para criar um modelo de regressão a fim de prever preços de tarifa de táxi. Esse processo chega ao melhor modelo aceitando dados de treinamento e definições de configuração, além de iterar automaticamente por meio de combinações de diferentes métodos, modelos e configurações de hiperparâmetros.

Neste tutorial, você aprenderá como:
- Baixar os dados usando o Apache Spark e o Azure Open Datasets.
- Transformar e limpar dados usando DataFrames do Apache Spark.
- Treinar um modelo de regressão com machine learning automatizado.
- Calcular a precisão do modelo.

## <a name="before-you-begin"></a>Antes de começar

- Crie um pool do Apache Spark sem servidor seguindo o [guia de início rápido Criar um pool do Apache Spark sem servidor](../quickstart-create-apache-spark-pool-studio.md).
- Conclua o tutorial de [instalação do Workspace do Azure Machine Learning](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) caso você não tenha um Workspace do Azure Machine Learning. 

## <a name="understand-regression-models"></a>Entender os modelos de regressão

*Os modelos de regressão* preveem valores de saída numéricos com base em preditores independentes. Na regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis de preditores independentes estimando como uma variável afeta as outras.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Exemplo baseado em dados de táxi da cidade de Nova York

Neste exemplo, você usa o Spark para executar uma análise de dados de gorjeta das corridas de táxi de NYC (cidade de Nova York). Os dados estão disponíveis por meio dos [Conjuntos de Dados em Aberto no Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Esse subconjunto do conjunto de dados contém informações sobre as corridas de táxi amarelo, incluindo informações sobre cada corrida, a hora de início e de término, além dos locais e o custo.

> [!IMPORTANT]
> Pode haver encargos adicionais para a extração desses dados no local de armazenamento. Nas etapas a seguir, você vai desenvolver um modelo para prever preços de tarifa de táxi de NYC. 

##  <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados

Aqui está como:

1. Crie um notebook usando o kernel do PySpark. Para obter instruções, confira [Criar um notebook](../quickstart-apache-spark-notebook.md#create-a-notebook).
   
    > [!Note]
    > Por causa do kernel do PySpark, não será necessário criar nenhum contexto explicitamente. O contexto do Spark é criado automaticamente para você ao executar a primeira célula do código.
  
2. Como os dados brutos estão no formato Parquet, você pode usar o contexto do Spark para extrair o arquivo diretamente na memória como um DataFrame. Crie um DataFrame do Spark recuperando os dados por meio da API do Open Datasets. Aqui, você usa as propriedades `schema on read` do DataFrame do Spark para inferir os tipos de dados e o esquema. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. Dependendo do tamanho do pool do Spark, os dados brutos podem ser muito grandes ou levar muito tempo para a operação. Você pode filtrar esses dados para obter algo menor, como o equivalente a um mês de dados, usando os filtros ```start_date``` e ```end_date```. Depois de filtrar um DataFrame, você também executa a função ```describe()``` no novo DataFrame para ver as estatísticas resumidas de cada campo. 

   Com base nas estatísticas resumidas, você pode ver que há algumas irregularidades nos dados. Por exemplo, as estatísticas mostram que a distância mínima de viagem é menor que zero. Será necessário filtrar esses pontos de dados irregulares.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Gere recursos do conjunto de dados selecionando um conjunto de colunas e criando vários recursos baseados em tempo relativos ao campo `datetime` do início da corrida. Filtre as exceções que foram identificadas na etapa anterior e remova as últimas colunas porque elas são desnecessárias para o treinamento.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Veja que isso criará um DataFrame com colunas adicionais para o dia do mês, a hora de início da corrida, o dia da semana e o tempo total de viagem. 

   ![Figura do DataFrame do táxi.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Gerar conjuntos de dados de testes e de validação

Depois de ter o conjunto de dados final, você pode dividir os data em conjuntos de treinamento e teste usando a função ```random_ split ``` no Spark. Usando os pesos fornecidos, essa função dividirá aleatoriamente os dados em: conjunto de dados de treinamento para o treinamento do modelo e no conjunto de dados de validação para teste.

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Essa etapa garante que os pontos de dados testem o modelo concluído que não foi usado para treinar o modelo. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Conectar a um workspace do Azure Machine Learning
No Azure Machine Learning, um workspace é uma classe que aceita suas informações de recursos e assinatura do Azure. Ele também cria um recurso de nuvem para monitorar e acompanhar a execução do seu modelo. Nesta etapa, você vai criar um objeto por meio do workspace do Azure Machine Learning existente.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Converter um DataFrame em um conjunto de dados do Azure Machine Learning
Para enviar um experimento remoto, converta seu conjunto de dados em uma instância de ```TabularDatset``` do Azure Machine Learning. Um [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) representa dados em um formato tabular, com base na análise dos arquivos fornecidos.

O código a seguir obtém o workspace existente e o armazenamento de dados do Azure Machine Learning. Em seguida, ele passa o armazenamento de dados e os locais de arquivo para que o parâmetro de caminho crie uma instância de ```TabularDataset```. 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Imagem do conjunto de dados carregado.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Enviar um experimento automatizado

As seções a seguir orientam você pelo processo de envio de um experimento de machine learning automatizado.

### <a name="define-training-settings"></a>Definir configurações de treinamento
1. Para enviar um experimento, você precisa definir as configurações de modelos e os parâmetros do experimento para o treinamento. Para obter a lista completa de configurações, consulte [Configurar experimentos de machine learning automatizado no Python](../../machine-learning/how-to-configure-auto-train.md).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Passe as configurações de treinamento definidas como um parâmetro `kwargs` para um objeto `AutoMLConfig`. Como você está usando o Spark, também deve passar o contexto do Spark, que é automaticamente acessível pela variável ```sc```. Além disso, você especifica os dados de treinamento e o tipo de modelo, que é a regressão nesse caso.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>As etapas de pré-processamento de machine learning automatizado se tornam parte do modelo subjacente. Essas etapas incluem normalização de recursos, manipulação de dados ausentes e conversão de texto em numérico. Quando você estiver usando o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento serão aplicadas automaticamente aos dados de entrada.

### <a name="train-the-automatic-regression-model"></a>Treinar o modelo de regressão automática 
Em seguida, você vai criar um objeto de experimento no workspace do Azure Machine Learning. Um experimento atua como um contêiner para suas execuções individuais. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Quando o experimento for concluído, a saída retornará detalhes sobre as iterações realizadas. Para cada iteração, você vê o tipo de modelo, a duração da execução e a precisão do treinamento. O campo `BEST` monitora a melhor pontuação de treinamento com base no tipo de métrica.

![Captura de tela da saída do modelo.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Depois de enviar o experimento de machine learning automatizado, ele executa várias iterações e tipos de modelo. Essa execução geralmente leva 60 a 90 minutos. 

### <a name="retrieve-the-best-model"></a>Recuperar o melhor modelo
Para selecionar o melhor modelo das iterações, use a função ```get_output``` para retornar o melhor modelo executado e ajustado. O código a seguir recupera o modelo de melhor execução e ajuste para qualquer métrica registrada em log ou uma iteração específica.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Testar a precisão do modelo
1. Para testar a precisão do modelo, use o melhor modelo para executar previsões de tarifa de táxi no conjunto de dados de teste. A função ```predict``` usa o melhor modelo e prevê os valores de `y` (valor da tarifa) com base no conjunto de dados de validação. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. A raiz do erro quadrático médio é uma medida usada com frequência das diferenças entre os valores da amostra previstos por um modelo e os valores observados. Você calcula a raiz do erro quadrático médio dos resultados comparando o DataFrame `y_test` com os valores previstos pelo modelo. 

   A função ```mean_squared_error``` usa duas matrizes e calcula o erro médio quadrático entre elas. Em seguida, você obtém a raiz quadrada do resultado. Essa métrica indica aproximadamente o quão distantes as previsões das tarifas de táxi estão dos valores de tarifa reais.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   A raiz do erro quadrático médio é uma boa medida da precisão da previsão da resposta do modelo. Nos resultados você vê que o modelo é razoavelmente bom em prever as tarifas de táxi com os recursos do conjunto de dados, normalmente dentro de US$ 2,00.

1. Execute o código a seguir para calcular o erro percentual absoluto médio. Essa métrica expressa a precisão como um percentual do erro. Ela faz isso calculando uma diferença absoluta entre cada valor previsto e real, somando todas as diferenças depois. Em seguida, ela expressa essa soma como um percentual do total de valores reais.

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Das duas métricas de precisão da previsão, você vê que o modelo é razoavelmente bom na previsão de tarifas de táxi por meio dos recursos do conjunto de dados. 

1. Depois de ajustar um modelo de regressão linear, agora você precisa determinar se o modelo se ajusta bem aos dados. Para fazer isso, você faz um gráfico dos valores reais de tarifa em relação à saída prevista. Além disso, você também calcula a medida R-quadrado para entender a proximidade dos dados com a linha de regressão ajustada.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![Captura de tela de um gráfico de regressão.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Com base nos resultados, você pode ver que a medida R-quadrado corresponde a 95% da variância. Isso também é validado pelo gráfico real versus o gráfico observado. Quanto mais variância for contabilizada pelo modelo de regressão, mais próximo os pontos de dados se encaixarão na linha de regressão ajustada.  

## <a name="register-the-model-to-azure-machine-learning"></a>Registrar o modelo no Azure Machine Learning
Depois de validar seu melhor modelo, você pode registrá-lo no Azure Machine Learning. Em seguida, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que registrou.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Exibir resultados no Azure Machine Learning
Você também pode acessar os resultados das iterações acessando o experimento no Workspace do Azure Machine Learning. Lá você pode obter mais detalhes sobre o status da execução, as tentativas de modelos e outras métricas do modelo. 

![Captura de tela de um Workspace do Azure Machine Learning.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Próximas etapas
- [Azure Synapse Analytics](../index.yml)
- [Tutorial: Criar um aplicativo de aprendizado de máquina com o Apache Spark MLlib e o Azure Synapse Analytics](./apache-spark-machine-learning-mllib-notebook.md)