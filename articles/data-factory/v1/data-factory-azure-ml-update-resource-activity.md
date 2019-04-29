---
title: Atualizar modelos de Machine Learning usando o Azure Data Factory | Microsoft Docs
description: Descreve como criar pipelines de previsão usando o Azure Data Factory e o Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0c0e0e3983344bba76f5f305ecaf73f91110f3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567278"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Atualizando os modelos do Machine Learning do Azure usando a Atividade de Recurso de Atualização

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade de Hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do Recurso de Atualização do Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade Personalizada do .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [atualizar modelos de aprendizado de máquina no Data Factory](../update-machine-learning-models.md).

Este artigo complementa o artigo principal sobre o Azure Data Factory - integração do Azure Machine Learning: [Criar pipelines de previsão usando Azure Machine Learning e o Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Se você ainda não fez isso, leia o artigo principal antes de ler este. 

## <a name="overview"></a>Visão geral
Ao longo do tempo, os modelos de previsão nos experimentos de pontuação do AM do Azure precisam ser treinados novamente usando novos conjuntos de dados de entrada. Depois de concluir o novo treinamento, você deseja atualizar o serviço Web de pontuação com o modelo do AM treinado novamente. As etapas típicas para habilitar novos treinamentos e a atualização de modelos do AM do Azure por meio de serviços Web são:

1. Crie um teste no [Azure ML Studio](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, use o Azure ML Studio para publicar serviços Web para o **teste de treinamento** e o teste de pontuação/**previsão**.

A tabela a seguir descreve os serviços Web usados neste exemplo.  Confira [Readaptar os modelos do Machine Learning de forma programática](../../machine-learning/machine-learning-retrain-models-programmatically.md) para obter detalhes.

- **Treinamento do serviço Web** - recebe dados de treinamento e produz modelos treinados. A saída do novo treinamento é um arquivo .ilearner em um Armazenamento de Blobs do Azure. O **ponto de extremidade padrão** é criado automaticamente para você quando o experimento de treinamento é publicado como um serviço Web. Você pode criar mais pontos de extremidade, mas o exemplo usa apenas o ponto de extremidade padrão.
- **Pontuação do serviço Web** - recebe exemplos de dados sem rótulo de e faz previsões. A saída de previsão pode ter diversas formas, como um arquivo .csv ou linhas em um banco de dados SQL do Azure, dependendo da configuração do experimento. O ponto de extremidade padrão é criado automaticamente para você quando o teste preditivo é publicado como um serviço Web. 

A figura a seguir descreve o relacionamento entre os pontos de extremidade de treinamento e de pontuação no AM do Azure.

![SERVIÇOS WEB](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Você pode invocar o **training web service** usando o **Atividade de Execução de Lote do AM do Azure**. A invocação de um serviço Web de treinamento é igual à invocação de um serviço Web do AM do Azure ML (serviço Web de pontuação) para pontuação de dados. As seções anteriores abordam em detalhes como invocar um serviço Web de AM do Azure de um pipeline do Azure Data Factory. 

Você pode invocar o **scoring web service** usando o **Atividade de Recurso de Atualização de AM do Azure** para atualizar o serviço Web com o modelo recém-treinado. Os exemplos a seguir fornecem as definições de serviço vinculado: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Serviço web de pontuação é um serviço web clássico
Se o serviço Web de pontuação é um **serviço Web clássico**, crie o segundo **ponto de extremidade não padrão e atualizável** usando o Portal do Azure. Confira o artigo [Criar pontos de extremidade](../../machine-learning/machine-learning-create-endpoint.md) para conhecer as etapas. Depois de criar o ponto de extremidade atualizável não padrão, execute as seguintes etapas:

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

Você pode obter valores para espaços reservados na URL ao consultar o serviço da web sobre o [Portal de serviços da Web do Azure Machine Learning](https://services.azureml.net/). O novo tipo de ponto de extremidade de recursos de atualização requer um token do AAD (Azure Active Directory). Especifique **servicePrincipalId** e **servicePrincipalKey**no serviço vinculado do AzureML. Consulte [como criar entidade de serviço e atribuir permissões para gerenciar recursos do Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Aqui está um exemplo de definição de serviço AzureML vinculado: 

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

O cenário a seguir fornece mais detalhes. Ele tem um exemplo de readaptação e atualização de modelos de AM do Azure de um pipeline do Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Cenário: novo treinamento e atualização de um modelo do AM do Azure
Esta seção fornece um pipeline de exemplo que usa a **atividade de Execução em lote do AM do Azure** para treinar novamente um modelo. O pipeline também usa a **atividade do Recurso de atualização do AM do Azure** para atualizar o modelo no serviço Web de pontuação. A seção também fornece snippets de JSON para todos os serviços vinculados, conjuntos de dados e pipeline no exemplo.

Este é o modo de exibição de diagrama do pipeline de exemplo. Como você pode ver, a atividade de Execução em lote do AM do Azure assume a entrada de treinamento e produz uma saída de treinamento (arquivo iLearner). A Atividade de Recurso de Atualização do AM do Azure obtém a saída deste treinamento e atualiza o modelo no ponto de extremidade de serviço Web de pontuação. A Atividade do Recurso de Atualização não produz nenhuma saída. O placeholderBlob é apenas um conjunto de dados de saída fictício necessário ao serviço Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de Blob do Azure:
O Armazenamento do Azure contém os seguintes dados:

* dados de treinamento. Os dados de entrada para o serviço Web de treinamento de AM do Azure.  
* Arquivo iLearner. Os dados de saída do serviço Web de treinamento de AM do Azure. Este arquivo também é a entrada para a atividade do Recurso de atualização.  

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
O conjunto de dados a seguir representa os dados de treinamento de entrada do serviço Web de treinamento do AM do Azure. A atividade de Execução de Lote do AM do Azure obtém esse conjunto de dados como uma entrada.

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
O conjunto de dados a seguir representa o arquivo do iLearner de saída do serviço Web de treinamento do AM do Azure. A Atividade de Execução de Lote do AM do Azure produz este conjunto de dados. O conjunto de dados também é a entrada para a atividade do Recurso de Atualização do AM do Azure.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Serviço vinculado para o ponto de extremidade de treinamento do AM do Azure ML
O snippet JSON a seguir define um serviço vinculado de Azure Machine Learning que aponta para o ponto de extremidade padrão do serviço Web de treinamento.

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

No **Azure ML Studio**, faça o seguinte para obter os valores de **mlEndpoint** e **apiKey**:

1. Clique em **SERVIÇOS WEB** no menu à esquerda.
2. Clique no **serviço Web de treinamento** na lista de serviços Web.
3. Clique em Copiar ao lado da caixa de texto **Chave de API** . Cole a chave na área de transferência e no editor JSON do Data Factory.
4. No **Azure ML Studio**, clique no link **EXECUÇÃO EM LOTE**.
5. Copie o **URI da Solicitação** da seção **Solicitação** e cole-o no editor de JSON do Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço Vinculado para o ponto de extremidade de pontuação atualizável do AM do Azure:
O snippet de código do JSON a seguir define um serviço vinculado do Azure Machine Learning que aponta para o ponto de extremidade não padrão atualizável do serviço Web de pontuação.  

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
A atividade de Atualização do recurso do AM do Azure não gera qualquer saída. No entanto, o Azure Data Factory exige um conjunto de dados de saída para gerar a agenda de um pipeline. Portanto, usamos um conjunto de dados fictício/espaço reservado neste exemplo.  

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
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de Execução em lote do AM do Azure usa os dados de treinamento como entrada e produz um arquivo iLearner como saída. A atividade invoca o serviço Web de treinamento (experimento de treinamento exposto como um serviço Web) com os dados de treinamento de entrada e recebe o arquivo ilearner do serviço Web. O placeholderBlob é apenas um conjunto de dados de saída fictício necessário ao serviço Azure Data Factory para executar o pipeline.

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
