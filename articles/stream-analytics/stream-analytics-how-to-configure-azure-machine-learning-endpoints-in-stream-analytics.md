---
title: Usar pontos de extremidade Azure Machine Learning Studio (clássico) no Azure Stream Analytics
description: Este artigo descreve como usar funções definidas pelo usuário de Machine Learning no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: a36162d24e18371fdf6b19835e4748e3043d1f24
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012518"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integração do Azure Machine Learning Studio (clássico) no Stream Analytics (versão prévia)
O Stream Analytics oferece suporte a funções definidas pelo usuário que chamam pontos de extremidade do Azure Machine Learning Studio (clássico). O suporte da API REST para esse recurso é detalhado na [biblioteca de API REST do Stream Analytics](/rest/api/streamanalytics/). Este artigo fornece informações complementares necessárias para a implementação bem-sucedida desse recurso no Stream Analytics. Um tutorial também foi publicado e está disponível [aqui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Visão geral: Terminologia do Azure Machine Learning Studio (clássico)
O Microsoft Azure Machine Learning Studio (clássico) oferece uma ferramenta colaborativa do tipo "arrastar e soltar", que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. Essa ferramenta é chamada *de Azure Machine Learning Studio (clássico)*. O estúdio (clássico) é usado para interagir com os recursos do Machine Learning e criar, testar e iterar facilmente no design. Veja abaixo esses recursos e suas definições.

* **Espaço de trabalho**: o *espaço de trabalho* é um contêiner que contém todos os outros recursos de aprendizado de máquina juntos em um contêiner para gerenciamento e controle.
* **Experimento**: Os *Experimentos* são criados por cientistas de dados a fim de usar conjuntos de dados e treinar um modelo de machine learning.
* **Endpoint**: os *pontos* de extremidade são o objeto Studio (clássico) usado para executar recursos como entrada, aplicar um modelo de Machine Learning especificado e retornar a saída pontuada.
* **Serviço Web de pontuação**: Um *serviço da Web de pontuação* é uma coleção de pontos de extremidade, conforme mencionado acima.

Cada ponto de extremidade tem APIs para execução em lote e execução síncrona. O Stream Analytics usa a execução síncrona. O serviço específico é chamado de [Serviço de Solicitação/Resposta](../machine-learning/classic/consume-web-services.md) no Azure Machine Learning Studio (clássico).

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Recursos de estúdio (clássico) necessários para trabalhos de Stream Analytics
Para que o processamento de trabalhos do Stream Analytics seja bem-sucedido, é necessário ter um ponto de extremidade de Solicitação/Resposta, uma [apikey](../machine-learning/classic/consume-web-services.md)e uma definição do Swagger. O Stream Analytics tem um ponto de extremidade adicional que constrói a URL do ponto de extremidade de swagger, procura a interface e retorna uma definição de UDF padrão para o usuário.

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>Configurar um UDF Stream Analytics e Studio (clássico) via API REST
Usando as APIs REST, você pode configurar seu trabalho para chamar funções do estúdio (clássico). As etapas são as seguintes:

1. Criar um trabalho de Stream Analytics
2. Definir uma entrada
3. Definir uma saída
4. Criar uma UDF (função definida pelo usuário)
5. Criar uma transformação do Stream Analytics que chama a UDF
6. Iniciar o trabalho

## <a name="creating-a-udf-with-basic-properties"></a>Criação de uma UDF com propriedades básicas
Por exemplo, o exemplo de código a seguir cria uma UDF escalar chamada *newudf* que realiza um vínculo a um ponto de extremidade de Azure Machine Learning Studio (clássico). Observe que o *ponto de extremidade* (URI de serviço) pode ser encontrado na página de ajuda da API do serviço escolhido e a *apiKey* pode ser encontrada na página principal de Serviços.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Exemplo de corpo de solicitação:

```json
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
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chamar o ponto de extremidade RetrieveDefaultDefinition para UDF padrão
Após a criação do esqueleto da UDF, é necessário obter a definição completa da UDF. O ponto de extremidade RetrieveDefaultDefinition ajuda a obter a definição padrão para uma função de valor escalar associada a um ponto de extremidade do Azure Machine Learning Studio (clássico). A carga abaixo exige que você obtenha a definição de UDF padrão para uma função escalar associada a um ponto de extremidade de estúdio (clássico). Ele não especifica o ponto de extremidade real, pois já foi fornecido durante a solicitação PUT. O Stream Analytics chamará o ponto de extremidade fornecido na solicitação se ele for fornecido explicitamente. Caso contrário, ele usa o ponto referenciado originalmente. Neste exemplo, a UDF usa um parâmetro de cadeia de caracteres única (uma sentença) e retorna uma saída única do tipo de cadeia de caracteres que indica o rótulo "sentimento" daquela sentença.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Exemplo de corpo de solicitação:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Um exemplo disso teria uma aparência como esta.

```json
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
```

## <a name="patch-udf-with-the-response"></a>Aplicar patch à UDF com a resposta
Agora a UDF deve ser corrigida com a resposta anterior, conforme exibido abaixo.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo da solicitação (saída de RetrieveDefaultDefinition):

```json
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
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementar uma transformação do Stream Analytics que chama a UDF
Agora, consulte a UDF (chamada aqui de scoreTweet) para cada evento de entrada e grave uma resposta para esse evento em uma saída.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, veja nossa [página de perguntas e respostas da Microsoft sobre o Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)