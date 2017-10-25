---
title: Atualizar modelos do Machine Learning usando o Azure Data Factory | Microsoft Docs
description: "Descreve como criar pipelines de previsão usando o Azure Data Factory e o Machine Learning"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 062d82d1bb2126a5b135796a2b5fa00ccf02511d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Atualizar modelos do Azure Machine Learning usando a atividade de atualização de recursos
Este artigo complementa o principal Azure Data Factory - Artigo de integração do Azure Machine Learning: [Criar pipelines de previsão usando o Azure Machine Learning e o Azure Data Factory](transform-data-using-machine-learning.md). Se você ainda não fez isso, leia o artigo principal antes de ler este. 

## <a name="overview"></a>Visão geral
Como parte do processo de operacionalização de modelos do Azure Machine Learning, o modelo é treinado e salvo. Em seguida, use-o para criar um serviço Web predicativo. O serviço Web pode ser consumido nos sites, painéis e aplicativos móveis.

Os modelos que você cria usando o Machine Learning geralmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. Consulte [Treinar novamente um modelo do Machine Learning](../machine-learning/machine-learning-retrain-machine-learning-model.md) para obter detalhes de como você pode treinar novamente um modelo do Azure Machine Learning. 

A readaptação pode ocorrer com frequência. Com a atividade de execução em lotes e a atividade de atualização de recursos, você pode operacionalizar o modelo do Azure Machine Learning, trainando novamente e atualizando o serviço Web de previsão usando o Data Factory. 

A figura a seguir descreve a relação entre os serviços Web de treinamento e previsão. 

![SERVIÇOS WEB](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Atividade de atualização de recursos do Azure Machine Learning 

O trecho JSON a seguir define uma atividade de execução em lotes do Azure Machine Learning.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```




| Propriedade                      | Descrição                              | Obrigatório |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Nome da atividade no pipeline     | Sim      |
| description                   | Texto que descreve o que a atividade faz.  | Não       |
| type                          | Para a atividade de atualização de recursos do Azure Machine Learning, o tipo de atividade é **AzureMLUpdateResource**. | Sim      |
| linkedServiceName             | O serviço vinculado do Azure Machine Learning que contém a propriedade updateResourceEndpoint. | Sim      |
| trainedModelName              | O nome do módulo de modelo treinado no teste do serviço Web a ser atualizado | Sim      |
| trainedModelLinkedServiceName | Nome do serviço vinculado do Armazenamento do Azure mantendo o arquivo ilearner que é carregado pela operação de atualização | Sim      |
| trainedModelFilePath          | O caminho do arquivo relativo no trainedModelLinkedService para representar o arquivo ilearner que é carregado pela operação de atualização | Sim      |


## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos

Todo o processo de operacionalização de novo treinamento de um modelo e de atualização dos serviços Web de previsão envolve as seguintes etapas: 

- Invocar o **treinamento do serviço Web** usando a **atividade de execução em lotes**. Invocar um serviço Web de treinamento é o mesmo que invocar um serviço Web de previsão, descrito em [Create predictive pipelines using Azure Machine Learning and Data Factory Batch Execution activity](transform-data-using-machine-learning.md) (Criar pipelines de previsão usando o Azure Machine Learning e a atividade de execução em lotes do Data Factory). A saída do treinamento do serviço Web é um arquivo iLearner que você pode usar para atualizar o serviço Web de previsão. 
- Invoque a **atualização do ponto de extremidade do recurso** do **Serviço Web de previsão** usando a **atividade de atualização de recurso** para atualizar o serviço Web com o modelo recém-treinado. 

## <a name="azure-machine-learning-linked-service"></a>Serviço vinculado do Azure Machine Learning

Para que o fluxo de trabalho de ponta a ponta mencionado acima funcione, você precisa criar dois serviços vinculado do Azure Machine Learning: 

1. Um serviço vinculado do Azure Machine Learning para o serviço Web de treinamento. Esse serviço vinculado é usado pela atividade de execução em lotes da mesma maneira que é mencionada em [Create predictive pipelines using Azure Machine Learning and Data Factory Batch Execution activity](transform-data-using-machine-learning.md) (Criar pipelines de previsão usando o Azure Machine Learning e a atividade de execução em lotes do Data Factory). A diferença é que a saída do serviço Web de treinamento é um arquivo iLearner que é usado pela atividade de atualização de recursos para atualizar o serviço Web de previsão. 
2. Um serviço vinculado do Azure Machine Learning para o ponto de extremidade do recurso de atualização do serviço Web de previsão. Esse serviço vinculado é usado pela atividade de atualização de recursos para atualizar o serviço Web de previsão usando o arquivo iLearner retornado da etapa acima. 

Para o segundo serviço vinculado do Azure Machine Learning, a configuração é diferente quando o serviço Web do Azure Machine Learning é um serviço Web clássico ou é um novo serviço Web. As diferenças são discutidas separadamente nas seções a seguir. 

## <a name="web-service-is-a-classic-web-service"></a>O serviço Web é um serviço Web clássico
Se o serviço Web de previsão for um **serviço Web clássico**, crie o segundo **ponto de extremidade não padrão e atualizável** usando o [Portal do Azure](https://manage.windowsazure.com). Confira o artigo [Criar pontos de extremidade](../machine-learning/machine-learning-create-endpoint.md) para conhecer as etapas. Depois de criar o ponto de extremidade atualizável não padrão, execute as seguintes etapas:

* Clique em **EXECUÇÃO EM LOTE** para obter o valor do URI para a propriedade JSON **mlEndpoint**.
* Clique no link **ATUALIZAR RECURSO** para obter o valor do URI para a propriedade JSON **updateResourceEndpoint**. A chave de API está na própria página do ponto de extremidade (no canto inferior direito).

![ponto de extremidade atualizável](./media/update-machine-learning-models/updatable-endpoint.png)

Depois disso, use o seguinte exemplo de serviço vinculado para criar um novo serviço vinculado do Azure Machine Learning. O serviço vinculado usa o apiKey para autenticação.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint2"
            },
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>O serviço Web é um novo serviço Web do Azure Resource Manager 

Se o serviço web é o novo tipo de serviço da web que expõe um ponto de extremidade do Azure Resource Manager, você não precisa adicionar o segundo **não-padrão** ponto de extremidade. O **updateResourceEndpoint** no serviço vinculado está no formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Você pode obter valores para espaços reservados na URL ao consultar o serviço da web sobre o [Portal de serviços da Web do Azure Machine Learning](https://services.azureml.net/). 

O novo tipo de ponto de extremidade do recurso de atualização requer a autenticação da entidade de serviço. Para usar a autenticação da entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (Azure AD) e atribua a ela a função **Colaborador** ou **Proprietário** da assinatura ou do grupo de recursos ao qual o serviço Web pertence. Consulte [como criar entidade de serviço e atribuir permissões para gerenciar recursos do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

- ID do aplicativo
- Chave do aplicativo 
- ID do locatário

Aqui está uma definição do serviço vinculado de exemplo: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
            "type": "SecureString",
            "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

O cenário a seguir fornece mais detalhes. Ele tem um exemplo de readaptação e atualização de modelos de AM do Azure de um pipeline do Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exemplo: treinando novamente e atualizando um modelo do Azure Machine Learning

Esta seção fornece um pipeline de exemplo que usa a **atividade de Execução em lote do AM do Azure** para treinar novamente um modelo. O pipeline também usa a **atividade do Recurso de atualização do AM do Azure** para atualizar o modelo no serviço Web de pontuação. A seção também fornece trechos de JSON para todos os serviços vinculados, conjuntos de dados e pipeline no exemplo.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Serviço vinculado para o ponto de extremidade de treinamento do AM do Azure ML
O trecho JSON a seguir define um serviço vinculado de Azure Machine Learning que aponta para o ponto de extremidade padrão do serviço Web de treinamento.

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

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço vinculado para o ponto de extremidade de pontuação atualizável do Azure ML:
O trecho JSON a seguir define um serviço vinculado do Azure Machine Learning que aponta para o ponto de extremidade atualizável do serviço Web de pontuação.  

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

### <a name="pipeline"></a>Pipeline
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução em lotes usa os dados de treinamento como entrada e produz um arquivo iLearner como saída. Em seguida, a atividade de atualização de recursos usa esse arquivo iLearner para atualizar o serviço Web de previsão. 

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            }, 
                            "FilePath":"azuremltesting/input"
                        }, 
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference" 
                            }, 
                            "FilePath":"azuremltesting/input"
                        }        
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"   
                            }, 
                            "FilePath":"azuremltesting/output"
                        }        
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                                "type": "LinkedServiceReference",
                                "referenceName": "StorageLinkedService"
                            },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [ 
                    { 
                        "activity": "amlbeGetilearner", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ]

            }
        ]
    }
}
```
## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
