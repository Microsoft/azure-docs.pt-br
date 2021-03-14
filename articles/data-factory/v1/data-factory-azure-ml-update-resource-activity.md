---
title: Atualizar modelos de Machine Learning usando Azure Data Factory
description: Descreve como criar pipelines preditivas usando Azure Data Factory v1 e Azure Machine Learning Studio (clássico)
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7a27ed657ba21d9e2125df903b40d74cd81eacf6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379294"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Atualizando modelos de Azure Machine Learning Studio (clássico) usando atualizar atividade de recurso

> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de execução em lotes do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do recurso de atualização do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL da Análise Data Lake](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [atualizar modelos de aprendizado de máquina no Data Factory](../update-machine-learning-models.md).

Este artigo complementa o principal artigo de integração Azure Data Factory-Azure Machine Learning Studio (clássico): [criar pipelines preditivas usando Azure Machine Learning Studio (clássico) e Azure data Factory](data-factory-azure-ml-batch-execution-activity.md). Se você ainda não fez isso, leia o artigo principal antes de ler este. 

## <a name="overview"></a>Visão geral
Ao longo do tempo, os modelos de previsão nos experimentos de Pontuação Azure Machine Learning Studio (clássico) precisam ser retreinados usando novos conjuntos de dados de entrada. Depois de concluir o novo treinamento, você deseja atualizar o serviço Web de pontuação com o modelo do AM treinado novamente. As etapas típicas para habilitar o retreinamento e a atualização de modelos de estúdio (clássico) por meio de serviços Web são:

1. Crie um experimento no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, use Azure Machine Learning Studio (clássico) para publicar serviços Web para o teste de **treinamento** e a pontuação/**teste de previsão**.

A tabela a seguir descreve os serviços Web usados neste exemplo.  Consulte [readaptação de modelos de Azure Machine Learning Studio (clássico) programaticamente](../../machine-learning/classic/retrain-machine-learning-model.md) para obter detalhes.

- **Treinamento do serviço Web** - recebe dados de treinamento e produz modelos treinados. A saída do novo treinamento é um arquivo .ilearner em um Armazenamento de Blobs do Azure. O **ponto de extremidade padrão** é criado automaticamente para você quando o experimento de treinamento é publicado como um serviço Web. Você pode criar mais pontos de extremidade, mas o exemplo usa apenas o ponto de extremidade padrão.
- **Pontuação do serviço Web** - recebe exemplos de dados sem rótulo de e faz previsões. A saída da previsão pode ter várias formas, como um arquivo. csv ou linhas no banco de dados SQL do Azure, dependendo da configuração do experimento. O ponto de extremidade padrão é criado automaticamente para você quando o teste preditivo é publicado como um serviço Web. 

A imagem a seguir descreve a relação entre os pontos de extremidade de treinamento e pontuação no Azure Machine Learning Studio (clássico).

![Serviços da Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Você pode invocar o **serviço Web de treinamento** usando a **atividade de execução em lote Azure Machine Learning Studio (clássico)**. Invocar um serviço Web de treinamento é o mesmo que invocar um serviço Web Azure Machine Learning Studio (clássico) (serviço Web de pontuação) para dados de pontuação. As seções anteriores abordam como invocar um serviço Web Azure Machine Learning Studio (clássico) de um pipeline de Azure Data Factory em detalhes. 

Você pode invocar o **serviço Web de Pontuação** usando a **atividade de recurso de atualização Azure Machine Learning Studio (clássica)** para atualizar o serviço Web com o modelo treinado recentemente. Os exemplos a seguir fornecem as definições de serviço vinculado: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Serviço web de pontuação é um serviço web clássico
Se o serviço Web de pontuação for um **serviço Web clássico**, crie o segundo **ponto de extremidade não padrão e atualizável** usando o portal do Azure. Confira o artigo [Criar pontos de extremidade](../../machine-learning/classic/create-endpoint.md) para conhecer as etapas. Depois de criar o ponto de extremidade atualizável não padrão, execute as seguintes etapas:

* Clique em **EXECUÇÃO EM LOTE** para obter o valor do URI para a propriedade JSON **mlEndpoint**.
* Clique no link **ATUALIZAR RECURSO** para obter o valor do URI para a propriedade JSON **updateResourceEndpoint**. A chave de API está na própria página do ponto de extremidade (no canto inferior direito).

![ponto de extremidade atualizável](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

O exemplo a seguir fornece um exemplo de definição de JSON para o serviço AzureML vinculado. O serviço vinculado usa o apiKey para autenticação.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Serviço Web de pontuação é um serviço Web do Azure Resource Manager 
Se o serviço web é o novo tipo de serviço da web que expõe um ponto de extremidade do Azure Resource Manager, você não precisa adicionar o segundo **não-padrão** ponto de extremidade. O **updateResourceEndpoint** no serviço vinculado está no formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Você pode obter valores para colocar os contentores na URL ao consultar o serviço Web no [portal de serviços web Azure Machine Learning Studio (clássico)](https://services.azureml.net/). O novo tipo de ponto de extremidade de recursos de atualização requer um token do AAD (Azure Active Directory). Especifique **servicePrincipalName** e **servicePrincipalKey** no serviço vinculado do estúdio (clássico). Consulte [como criar entidade de serviço e atribuir permissões para gerenciar recursos do Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Aqui está um exemplo de definição de serviço AzureML vinculado: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

O cenário a seguir fornece mais detalhes. Ele tem um exemplo para retreinar e atualizar modelos de estúdio (clássico) de um pipeline Azure Data Factory.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Cenário: retreinar e atualizar um modelo de estúdio (clássico)
Esta seção fornece um pipeline de exemplo que usa a **atividade de execução de lote Azure Machine Learning Studio (clássica)** para treinar novamente um modelo. O pipeline também usa a **atividade de recurso de atualização Azure Machine Learning Studio (clássica)** para atualizar o modelo no serviço Web de pontuação. A seção também fornece snippets de JSON para todos os serviços vinculados, conjuntos de dados e pipeline no exemplo.

Este é o modo de exibição de diagrama do pipeline de exemplo. Como você pode ver, a atividade de execução em lote do estúdio (clássico) usa a entrada de treinamento e produz uma saída de treinamento (arquivo iLearner). A atividade de recurso de atualização do estúdio (clássico) usa essa saída de treinamento e atualiza o modelo no ponto de extremidade do serviço Web de pontuação. A Atividade do Recurso de Atualização não produz nenhuma saída. O placeholderBlob é apenas um conjunto de dados de saída fictício necessário ao serviço Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de Blob do Azure:
O Armazenamento do Azure contém os seguintes dados:

* dados de treinamento. Os dados de entrada para o serviço Web de treinamento do Studio (clássico).  
* Arquivo iLearner. A saída do serviço Web de treinamento do Studio (clássico). Este arquivo também é a entrada para a atividade do Recurso de atualização.  

Veja a definição JSON de exemplo do serviço vinculado:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Conjunto de dados de entrada do treinamento:
O DataSet a seguir representa os dados de treinamento de entrada para o serviço Web de treinamento do Studio (clássico). A atividade de execução em lote do estúdio (clássico) usa esse conjunto de dados como uma entrada.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Conjunto de dados de saída de treinamento:
O conjunto de resultados a seguir representa o arquivo iLearner de saída do serviço Web de treinamento Azure Machine Learning Studio (clássico). A atividade de execução de lote Azure Machine Learning Studio (clássica) produz esse conjunto de DataSet. Esse conjunto de dados também é a entrada para a atividade de recurso de atualização Azure Machine Learning Studio (clássica).

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Serviço vinculado para o ponto de extremidade de treinamento do Studio (clássico)
O trecho JSON a seguir define um serviço vinculado de estúdio (clássico) que aponta para o ponto de extremidade padrão do serviço Web de treinamento.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

Em **Azure Machine Learning Studio (clássico)**, faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique em **SERVIÇOS WEB** no menu à esquerda.
2. Clique no **serviço Web de treinamento** na lista de serviços Web.
3. Clique em Copiar ao lado da caixa de texto **Chave de API** . Cole a chave na área de transferência e no editor JSON do Data Factory.
4. No **Azure Machine Learning Studio (clássico)**, clique no link **execução em lote** .
5. Copie o **URI da Solicitação** da seção **Solicitação** e cole-o no editor de JSON do Data Factory.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Serviço vinculado para o ponto de extremidade de Pontuação atualizável do Studio (clássico):
O trecho JSON a seguir define um serviço vinculado de estúdio (clássico) que aponta para o ponto de extremidade atualizável não padrão do serviço Web de pontuação.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Conjunto de dados de saída de espaço reservado:
A atividade de recurso de atualização do estúdio (clássico) não gera nenhuma saída. No entanto, o Azure Data Factory exige um conjunto de dados de saída para gerar a agenda de um pipeline. Portanto, usamos um conjunto de dados fictício/espaço reservado neste exemplo.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lote Azure Machine Learning Studio (clássica) usa os dados de treinamento como entrada e produz um arquivo iLearner como uma saída. A atividade invoca o serviço Web de treinamento (experimento de treinamento exposto como um serviço Web) com os dados de treinamento de entrada e recebe o arquivo ilearner do serviço Web. O placeholderBlob é apenas um conjunto de dados de saída fictício necessário ao serviço Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```