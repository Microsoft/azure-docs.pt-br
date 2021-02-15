---
title: Atualizar modelos Azure Machine Learning Studio (clássico) usando Azure Data Factory
description: Descreve como criar pipelines preditivas usando Azure Data Factory e Azure Machine Learning Studio (clássico)
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: ef89ecef34a7c5afb94547181f449b0fc393e67c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377560"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Atualizar modelos de Azure Machine Learning Studio (clássico) usando atualizar atividade de recurso

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo complementa o principal artigo de integração Azure Data Factory-Azure Machine Learning Studio (clássico): [criar pipelines preditivas usando Azure Machine Learning Studio (clássico) e Azure data Factory](transform-data-using-machine-learning.md). Se você ainda não fez isso, leia o artigo principal antes de ler este.

## <a name="overview"></a>Visão geral
Como parte do processo de operacionalização de modelos Azure Machine Learning Studio (clássico), seu modelo é treinado e salvo. Em seguida, será possível utilizá-lo para criar um serviço Web preditivo. O serviço Web pode ser consumido nos sites, painéis e aplicativos móveis.

Os modelos criados usando Azure Machine Learning Studio (clássico) normalmente não são estáticos. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser readaptado. 

A readaptação pode ocorrer com frequência. Com a atividade de execução em lote e a atividade atualizar recurso, você pode colocar em operação o modelo de Azure Machine Learning Studio (clássico) retreinando e atualizando o serviço Web de previsão usando Data Factory.

A figura a seguir descreve a relação entre os serviços Web de treinamento e previsão.

![Serviços da Web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Atividade de recurso de atualização Azure Machine Learning Studio (clássica)

O trecho JSON a seguir define uma atividade de execução de lote Azure Machine Learning Studio (clássica).

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
| descrição                   | Texto que descreve o que a atividade faz.  | Não       |
| type                          | Para Azure Machine Learning Studio atividade de recurso de atualização (clássica), o tipo de atividade é  **AzureMLUpdateResource**. | Sim      |
| linkedServiceName             | Azure Machine Learning Studio serviço vinculado (clássico) que contém a propriedade updateResourceEndpoint. | Sim      |
| trainedModelName              | O nome do módulo de modelo treinado no teste do serviço Web a ser atualizado | Sim      |
| trainedModelLinkedServiceName | Nome do serviço vinculado do Armazenamento do Azure mantendo o arquivo ilearner que é carregado pela operação de atualização | Sim      |
| trainedModelFilePath          | O caminho do arquivo relativo no trainedModelLinkedService para representar o arquivo ilearner que é carregado pela operação de atualização | Sim      |

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos

Todo o processo de operacionalização de novo treinamento de um modelo e de atualização dos serviços Web de previsão envolve as seguintes etapas:

- Invocar o **treinamento do serviço Web** usando a **atividade de execução em lotes**. Invocar um serviço Web de treinamento é o mesmo que invocar um serviço Web de previsão descrito em [criar pipelines preditivas usando Azure Machine Learning Studio (clássico) e data Factory atividade de execução em lote](transform-data-using-machine-learning.md). A saída do serviço Web de treinamento é um arquivo iLearner que você pode usar para atualizar o serviço Web de previsão.
- Invoque a **atualização do ponto de extremidade do recurso** do **Serviço Web de previsão** usando a **atividade de atualização de recurso** para atualizar o serviço Web com o modelo recém-treinado.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Serviço vinculado do Azure Machine Learning Studio (clássico)

Para que o fluxo de trabalho de ponta a ponta mencionado acima funcione, você precisa criar dois serviços vinculados Azure Machine Learning Studio (clássico):

1. Um serviço vinculado Azure Machine Learning Studio (clássico) para o serviço Web de treinamento, esse serviço vinculado é usado pela atividade de execução em lote da mesma forma como o que é mencionado em [criar pipelines preditivas usando Azure Machine Learning Studio (clássico) e data Factory atividade de execução em lote](transform-data-using-machine-learning.md). A diferença é que a saída do serviço Web de treinamento é um arquivo iLearner, que é usado pela atividade atualizar recurso para atualizar o serviço Web de previsão.
2. Um serviço vinculado Azure Machine Learning Studio (clássico) para o ponto de extremidade de recurso de atualização do serviço Web de previsão. Esse serviço vinculado é usado pela atividade de atualização de recursos para atualizar o serviço Web de previsão usando o arquivo iLearner retornado da etapa acima.

Para o segundo serviço vinculado do Azure Machine Learning Studio (clássico), a configuração é diferente quando o serviço Web Azure Machine Learning Studio (clássico) é um serviço Web clássico ou um novo serviço Web. As diferenças são discutidas separadamente nas seções a seguir.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>O serviço Web é um novo serviço Web do Azure Resource Manager

Se o serviço web é o novo tipo de serviço da web que expõe um ponto de extremidade do Azure Resource Manager, você não precisa adicionar o segundo **não-padrão** ponto de extremidade. O **updateResourceEndpoint** no serviço vinculado está no formato:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Você pode obter valores para colocar os contentores na URL ao consultar o serviço Web no [portal de serviços web Azure Machine Learning Studio (clássico)](https://services.azureml.net/).

O novo tipo de ponto de extremidade do recurso de atualização requer a autenticação da entidade de serviço. Para usar a autenticação da entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (Azure AD) e atribua a ela a função **Colaborador** ou **Proprietário** da assinatura ou do grupo de recursos ao qual o serviço Web pertence. Consulte [como criar entidade de serviço e atribuir permissões para gerenciar recursos do Azure](../active-directory/develop/howto-create-service-principal-portal.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

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
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
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

O cenário a seguir fornece mais detalhes. Ele tem um exemplo para retreinar e atualizar os modelos de Azure Machine Learning Studio (clássico) de um pipeline de Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>Exemplo: retreinando e atualizando um modelo de Azure Machine Learning Studio (clássico)

Esta seção fornece um pipeline de exemplo que usa a **atividade de execução de lote Azure Machine Learning Studio (clássica)** para treinar novamente um modelo. O pipeline também usa a **atividade de recurso de atualização Azure Machine Learning Studio (clássica)** para atualizar o modelo no serviço Web de pontuação. A seção também fornece snippets de JSON para todos os serviços vinculados, conjuntos de dados e pipeline no exemplo.

### <a name="azure-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de Blob do Azure:
O Armazenamento do Azure contém os seguintes dados:

* dados de treinamento. Os dados de entrada para o serviço Web de treinamento Azure Machine Learning Studio (clássico).
* Arquivo iLearner. A saída do serviço Web de treinamento Azure Machine Learning Studio (clássico). Este arquivo também é a entrada para a atividade do Recurso de atualização.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Serviço vinculado para ponto de extremidade de treinamento Azure Machine Learning Studio (clássico)
O trecho JSON a seguir define um serviço vinculado Azure Machine Learning Studio (clássico) que aponta para o ponto de extremidade padrão do serviço Web de treinamento.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Serviço vinculado para ponto de extremidade de Pontuação atualizável Azure Machine Learning Studio (clássico):
O trecho JSON a seguir define um serviço vinculado Azure Machine Learning Studio (clássico) que aponta para o ponto de extremidade atualizável do serviço Web de pontuação.

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
