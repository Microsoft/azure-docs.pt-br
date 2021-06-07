---
title: Acompanhamento de MLflow para experimentos de ML Azure Databricks
titleSuffix: Azure Machine Learning
description: Configure o MLflow com Azure Machine Learning para registrar métricas e artefatos de Azure Databricks experimentos do ml.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a894a46ce7c78b65dde80c52f9516fcbe4d27bcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520992"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Acompanhe os experimentos Azure Databricks ML com MLflow e Azure Machine Learning (visualização)

Neste artigo, saiba como habilitar o URI de acompanhamento do MLflow e a API de log, coletivamente conhecido como [acompanhamento de MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), para conectar seus experimentos de Azure DATABRICKS (ADB), MLflow e Azure Machine Learning.

O [MLflow](https://www.mlflow.org) é uma biblioteca de open-source para gerenciar o ciclo de vida dos experimentos de aprendizado de máquina. O acompanhamento de MLFlow é um componente de MLflow que registra e rastreia suas métricas de execução de treinamento e artefatos de modelo. Saiba mais sobre [Azure Databricks e MLflow](/azure/databricks/applications/mlflow/). 

Consulte [acompanhar execuções de experimento com MLflow e Azure Machine Learning](how-to-use-mlflow.md) para obter MLflow adicionais e integrações de funcionalidade de Azure Machine Learning.

>[!NOTE]
> Como uma biblioteca de software livre, o MLflow muda com frequência. Dessa forma, a funcionalidade disponibilizada por meio da integração de Azure Machine Learning e MLflow deve ser considerada uma versão prévia e não tem suporte total da Microsoft.

> [!TIP]
> Este documento oferece informações principalmente para cientistas de dados e desenvolvedores que querem monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Instale o pacote `azureml-mlflow`. 
    * Esse pacote automaticamente traz a `azureml-core` [Azure Machine Learning SDK do Python](/python/api/overview/azure/ml/install), que fornece a conectividade para MLflow acessar seu espaço de trabalho.
* Um [Azure Databricks espaço de trabalho e cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).
    * Veja quais [permissões de acesso você precisa para executar suas operações MLflow com seu espaço de trabalho](how-to-assign-roles.md#mlflow-operations).

## <a name="track-azure-databricks-runs"></a>Acompanhar as execuções do Azure Databricks

O acompanhamento de MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos do seu Azure Databricks é executado em ambos: 

* Espaço de trabalho Azure Databricks.
* Workspace do Azure Machine Learning

Depois de criar seu Azure Databricks espaço de trabalho e cluster, 

1. Instale a biblioteca *azureml-mlflow* de PyPi para garantir que o cluster tenha acesso às funções e classes necessárias.

1. Configure seu notebook de experimento.

1. Conecte seu espaço de trabalho do Azure Databricks e Azure Machine Learning espaço de trabalho.

Detalhes adicionais para essas etapas estão nas seções a seguir para que você possa executar seus experimentos do MLflow com êxito com Azure Databricks. 

## <a name="install-libraries"></a>Instalar bibliotecas

Para instalar bibliotecas em seu cluster, navegue até a guia **bibliotecas** e selecione **Instalar nova**

 ![mlflow com o Azure databricks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

No campo **pacote** , digite azureml-mlflow e, em seguida, selecione instalar. Repita essa etapa conforme necessário para instalar outros pacotes no cluster do experimento.

 ![Biblioteca mlflow de instalação do BD do Azure](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Configurar seu notebook 

Quando o cluster ADB estiver configurado, 
1. Selecione **espaços de trabalho** no painel de navegação esquerdo. 
1. Expanda o menu suspenso espaços de trabalho e selecione **importar**
1. Arraste e solte ou navegue até localizar, seu notebook de experimento para importar seu espaço de trabalho ADB.
1. Selecione **Importar**. Seu notebook de experimento é aberto automaticamente.
1. No título do bloco de anotações na parte superior esquerda, selecione o cluster que deseja anexar ao seu notebook de experimento. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Conectar os espaços de trabalho do Azure Databricks e do Azure Machine Learning

Vincular seu espaço de trabalho ADB ao seu espaço de trabalho do Azure Machine Learning permite que você acompanhe seus dados de teste no espaço de trabalho Azure Machine Learning.

Para vincular seu espaço de trabalho ADB a um espaço de trabalho Azure Machine Learning novo ou existente, 
1. Entre no [Portal do Azure](https://ms.portal.azure.com).
1. Navegue até a página **visão geral** do espaço de trabalho do ADB.
1. Selecione o botão **vincular Azure Machine Learning espaço de trabalho** no canto inferior direito. 

 ![Vincular o BD do Azure e espaços de trabalho do Azure Machine Learning](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>Acompanhamento de MLflow em seus espaços de trabalho

Depois de instanciar seu espaço de trabalho, o acompanhamento de MLflow é definido automaticamente para ser acompanhado em todos os seguintes locais:

* O espaço de trabalho Azure Machine Learning vinculado.
* Seu espaço de trabalho do ADB original. 

Todos os experimentos se esterram no serviço de controle de Azure Machine Learning gerenciado.

O código a seguir deve estar no seu notebook de teste para obter seu espaço de trabalho de Azure Machine Learning vinculado. 

Esse código, 

*  Obtém os detalhes da sua assinatura do Azure para instanciar seu espaço de trabalho Azure Machine Learning. 

* Pressupõe que você tenha um grupo de recursos existente e Azure Machine Learning espaço de trabalho, caso contrário, você pode [criá-los](how-to-manage-workspace.md). 

* Define o nome do experimento. `user_name`Aqui está consistente com o `user_name` associado ao espaço de trabalho Azure Databricks.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Definir o acompanhamento de MLflow para rastrear somente em seu espaço de trabalho Azure Machine Learning

Se preferir gerenciar seus experimentos controlados em um local centralizado, você poderá definir o acompanhamento de MLflow para rastrear **somente** em seu espaço de trabalho Azure Machine Learning. 

Inclua o seguinte código em seu script:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

No script de treinamento, importe `mlflow` para usar as APIs de registro do MLflow e comece a registrar as métricas de execução. O exemplo a seguir registra a métrica de perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Registrar modelos com MLflow

Depois que o modelo é treinado, você pode registrar e registrar seus modelos no servidor de acompanhamento de back-end com o `mlflow.<model_flavor>.log_model()` método. `<model_flavor>`, refere-se à estrutura associada ao modelo. [Saiba quais são os tipos de modelo com suporte](https://mlflow.org/docs/latest/models.html#model-api). 

O servidor de controle de back-end é o espaço de trabalho Azure Databricks por padrão; a menos que você opte por [definir o acompanhamento de MLflow para rastrear somente em seu espaço de trabalho Azure Machine Learning](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), o servidor de acompanhamento de back-end é o espaço de trabalho Azure Machine Learning.   

* **Se um modelo registrado com o nome não existir**, o método registrará um novo modelo, criará a versão 1 e retornará um objeto ModelVersion MLflow. 

* **Se um modelo registrado com o nome já existir**, o método criará uma nova versão de modelo e retornará o objeto de versão. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Criar pontos de extremidade para modelos MLflow

Quando você estiver pronto para criar um ponto de extremidade para seus modelos de ML. Você pode implantar como, 

* Um Azure Machine Learning Request-Response Web Service para Pontuação interativa. Essa implantação permite aproveitar e aplicar o gerenciamento de modelos de Azure Machine Learning e os recursos de detecção de descompasso de dados para seus modelos de produção. 

* Objetos de modelo MLFlow, que podem ser usados em pipelines de streaming ou lote como funções Python ou pandas UDFs no espaço de trabalho Azure Databricks.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Implantar modelos para Azure Machine Learning pontos de extremidade 
Você pode aproveitar a API [mlflow. azureml. Deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) para implantar um modelo em seu espaço de trabalho do Azure Machine Learning. Se você registrou apenas o modelo no espaço de trabalho Azure Databricks, conforme descrito na seção [registrar modelos com MLflow](#register-models-with-mlflow) , especifique o `model_name` parâmetro para registrar o modelo em Azure Machine Learning espaço de trabalho. 

Azure Databricks execuções podem ser implantadas nos pontos de extremidade a seguir, 
* [Instância de contêiner do Azure](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Serviço de Kubernetes do Azure](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Implantar modelos em pontos de extremidade de ADB para Pontuação de lote 

Você pode escolher clusters de Azure Databricks para Pontuação de lote. O modelo MLFlow é carregado e usado como um UDF do Spark pandas para pontuar novos dados. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não planeja usar as métricas registradas e os artefatos em seu espaço de trabalho, a capacidade de excluí-los individualmente não estará disponível no momento. Em vez disso, exclua o grupo de recursos que contém a conta de armazenamento e o espaço de trabalho, para que você não incorra nenhum encargo:

1. No portal do Azure, selecione **Grupos de recursos** no canto esquerdo.

   ![Exclusão no portal do Azure](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Selecione o grupo de recursos criado na lista.

1. Selecione **Excluir grupo de recursos**.

1. Insira o nome do grupo de recursos. Em seguida, selecione **Excluir**.

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

O [MLflow com blocos de anotações Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstram e se expandem sobre os conceitos apresentados neste artigo.

## <a name="next-steps"></a>Próximas etapas
* [Implantar modelos MLflow como um serviço Web do Azure](how-to-deploy-mlflow-models.md). 
* [Gerenciar os modelos](concept-model-management-and-deployment.md).
* [Acompanhe execuções de experimento com MLflow e Azure Machine Learning](how-to-use-mlflow.md). 
* Saiba mais sobre [Azure Databricks e MLflow](/azure/databricks/applications/mlflow/).