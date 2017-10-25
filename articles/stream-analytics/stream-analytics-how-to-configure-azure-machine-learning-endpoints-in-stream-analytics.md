---
title: Use pontos de extremidade do Azure Machine Learning no Stream Analytics | Microsoft Docs
description: "Funções definidas pelo usuário da Linguagem de Máquina no Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 406b258f-b8c2-4e55-953c-b7f84e8e5354
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: ca656ce11f267340872f4b8566d0ee21791f29d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Integração do Machine Learning ao Stream Analytics
O Stream Analytics dá suporte a funções definidas pelo usuário que chamam pontos de extremidade do Azure Machine Learning. O suporte da API REST para esse recurso é detalhado na [biblioteca de API REST do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Este artigo fornece informações complementares necessárias para a implementação bem-sucedida desse recurso no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Visão geral: terminologia do Azure Machine Learning
O Microsoft Azure Machine Learning fornece uma ferramenta colaborativa do tipo "arrastar e soltar", que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. Essa ferramenta é chamada de *Azure Machine Learning Studio*. O estúdio é usado para interagir com os recursos do Machine Learning e para compilar, testar e iterar facilmente em seu design. Veja abaixo esses recursos e suas definições.

* **Espaço de trabalho**: o *espaço de trabalho* é um contêiner que mantém todos os outros recursos de Machine Learning em um contêiner para gerenciamento e controle.
* **Experimento**: os *Experimentos* são criados por cientistas de dados a fim de usar conjuntos de dados e treinar um modelo de aprendizado de máquina.
* **Ponto de extremidade**: os *Pontos de extremidade* são objetos de Azure Machine Learning usados para aproveitar recursos como entrada, aplicar um modelo de aprendizado de máquina especificado e retornar a saída pontuada.
* **Serviço da Web de Pontuação**: um *serviço da Web de pontuação* é uma coleção de pontos de extremidade, conforme mencionado acima.

Cada ponto de extremidade tem APIs para execução em lote e execução síncrona. O Stream Analytics usa a execução síncrona. O serviço específico é chamado de [Serviço de Solicitação/Resposta](../machine-learning/studio/consume-web-services.md) no Estúdio AM do Azure.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Recursos de Machine Learning necessários para trabalhos do Stream Analytics
Para que o processamento de trabalhos do Stream Analytics seja bem-sucedido, é necessário ter um ponto de extremidade de Solicitação/Resposta, uma [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)e uma definição do Swagger. O Stream Analytics tem um ponto de extremidade adicional que constrói a URL do ponto de extremidade de swagger, procura a interface e retorna uma definição de UDF padrão para o usuário.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurar uma UDF de Stream Analytics e Machine Learning por meio da API REST
Com as APIs REST, você pode configurar seu trabalho para chamar funções de Linguagem de Máquina do Azure. As etapas são as seguintes:

1. Criar um trabalho de Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma UDF (função definida pelo usuário)
5. Criar uma transformação do Stream Analytics que chama a UDF
6. Iniciar o trabalho

## <a name="creating-a-udf-with-basic-properties"></a>Criação de uma UDF com propriedades básicas
Por exemplo, o exemplo de código a seguir cria uma UDF escalar chamada *newudf* que realiza um vínculo a um ponto de extremidade de Azure Machine Learning. Observe que o *ponto de extremidade* (URI de serviço) pode ser encontrado na página de ajuda da API do serviço escolhido e a *apiKey* pode ser encontrada na página principal de Serviços.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Exemplo de corpo de solicitação:  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chamar o ponto de extremidade RetrieveDefaultDefinition para UDF padrão
Após a criação do esqueleto da UDF, é necessário obter a definição completa da UDF. O ponto de extremidade RetreiveDefaultDefinition ajuda a obter a definição padrão para uma função escalar associada a um ponto de extremidade de Azure Machine Learning. A carga abaixo exige que você obtenha definição padrão da UDF para uma função escalar associada a um ponto de extremidade de Azure Machine Learning. Ela não especifica o ponto de extremidade real, pois ele já foi fornecido durante a solicitação PUT. O Stream Analytics chamará o ponto de extremidade fornecido na solicitação se ele for fornecido explicitamente. Caso contrário, ele usa o ponto referenciado originalmente. Neste exemplo, a UDF usa um parâmetro de cadeia única (uma sentença) e retorna uma única saída do tipo de cadeia de caracteres que indica o rótulo "sentimento" daquela sentença.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Exemplo de corpo de solicitação:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Um exemplo disso teria uma aparência como esta.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Aplicar patch à UDF com a resposta
Agora a UDF deve ser corrigida com a resposta anterior, conforme exibido abaixo.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corpo da solicitação (saída de RetrieveDefaultDefinition):

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar uma transformação do Stream Analytics que chama a UDF
Agora, consulte a UDF (chamada aqui de scoreTweet) para cada evento de entrada e grave uma resposta para esse evento em uma saída.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
