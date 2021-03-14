---
title: Criar cliente para modelo implantado como serviço Web
titleSuffix: Azure Machine Learning
description: Saiba como chamar um ponto de extremidade de serviço Web que foi gerado quando um modelo foi implantado de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: e9fb801fce3e47fc83febeddd6f331ce2af207e6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506966"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Consumir um modelo de Azure Machine Learning implantado como um serviço web


A implantação de um modelo do Azure Machine Learning como um serviço Web cria um ponto de extremidade da API REST. Você pode enviar dados para esse ponto de extremidade e receber a previsão retornada pelo modelo. Neste documento, saiba como criar clientes para o serviço da Web usando C#, Go, Java e Python.

Você cria um serviço Web ao implantar um modelo em seu ambiente local, instâncias de contêiner do Azure, serviço kubernetes do Azure ou FPGA (matrizes de portão programável por campo). Você recupera o URI usado para acessar o serviço Web usando o [SDK do Azure Machine Learning](/python/api/overview/azure/ml/intro). Se a autenticação estiver habilitada, você também poderá usar o SDK para obter as chaves de autenticação ou os tokens.

O fluxo de trabalho geral para a criação de um cliente que usa um serviço web machine learning é:

1. Use o SDK para obter as informações de conexão.
1. Determinar o tipo de dados de solicitação usados pelo modelo.
1. Crie um aplicativo que chame o serviço da web.

> [!TIP]
> Os exemplos neste documento são criados manualmente sem o uso de especificações de OpenAPI (Swagger). Se você habilitou uma especificação OpenAPI para sua implantação, poderá usar ferramentas como o [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen) para criar bibliotecas de cliente para seu serviço.

## <a name="connection-information"></a>Informações de conexão

> [!NOTE]
> Use o SDK do Azure Machine Learning para obter as informações do serviço Web. Esse é um SDK de Python. Você pode usar qualquer linguagem para criar um cliente para o serviço.

A classe [azureml.core.Webservice](/python/api/azureml-core/azureml.core.webservice%28class%29) fornece as informações necessárias para criar um cliente. As seguintes propriedades `Webservice` que são úteis para criar um aplicativo cliente:

* `auth_enabled` -Se a autenticação de chave estiver habilitada, `True` caso contrário, `False` .
* `token_auth_enabled` -Se a autenticação de token estiver habilitada, `True` caso contrário, `False` .
* `scoring_uri` -O endereço da API REST.
* `swagger_uri` -O endereço da especificação OpenAPI. Esse URI estará disponível se você tiver habilitado a geração de esquema automática. Para obter mais informações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

Há várias maneiras de recuperar essas informações para serviços Web implantados:

# <a name="python"></a>[Python](#tab/python)

* Quando você implanta um modelo, um objeto `Webservice` é retornado com informações sobre o serviço:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Você pode usar `Webservice.list` para recuperar uma lista de serviços da Web implementados para modelos em seu workspace. Você pode adicionar filtros para restringir a lista de informações retornadas. Para obter mais informações sobre o que pode ser filtrado, consulte a documentação de referência do [Webservice.list](/python/api/azureml-core/azureml.core.webservice.webservice.webservice).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Se você souber o nome do serviço implantado, você pode criar uma nova instância de `Webservice` e fornecer o nome do workspace e o serviço como parâmetros. O novo objeto contém informações sobre o serviço implantado.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você souber o nome do serviço implantado, use o comando [AZ ml Service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) :

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

No Azure Machine Learning Studio, selecione __pontos__ de extremidade, __pontos de extremidades em tempo real__ e, em seguida, o nome do ponto de extremidades. Em detalhes do ponto de extremidade, o campo __ponto de extremidade REST__ contém o URI de pontuação. O __URI do Swagger__ contém o URI do Swagger.

---

A tabela a seguir mostra como esses URIs se parecem:

| Tipo de URI | Exemplo |
| ----- | ----- |
| URI de Pontuação | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| URI do Swagger | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> O endereço IP será diferente para a sua implantação. Cada cluster AKS terá seu próprio endereço IP que é compartilhado por implantações nesse cluster.

### <a name="secured-web-service"></a>Serviço Web protegido

Se você protegeu o serviço Web implantado usando um certificado TLS/SSL, você pode usar [https](https://en.wikipedia.org/wiki/HTTPS) para se conectar ao serviço usando a pontuação ou o URI do Swagger. O HTTPS ajuda a proteger as comunicações entre um cliente e um serviço Web criptografando as comunicações entre os dois. A criptografia usa [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Às vezes, o TLS ainda é referido como *protocolo SSL* (SSL), que era o PREDECESSOR do TLS.

> [!IMPORTANT]
> Os serviços Web implantados pelo Azure Machine Learning só oferecem suporte a TLS versão 1,2. Ao criar um aplicativo cliente, verifique se ele dá suporte a essa versão.

Para obter mais informações, consulte [Usar o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Autenticação para serviços

O Azure Machine Learning fornece duas maneiras de controlar o acesso aos serviços Web.

|Método de autenticação|ACI|AKS|
|---|---|---|
|Chave|Desabilitadas por padrão| Habilitado por padrão|
|Token| Não disponível| Desabilitado por padrão |

Ao enviar uma solicitação para um serviço protegido com uma chave ou token, use o cabeçalho de __autorização__ para passar a chave ou o token. A chave ou o token devem ser formatados como `Bearer <key-or-token>` , em que `<key-or-token>` é o valor da chave ou do token.

A principal diferença entre chaves e tokens é que **as chaves são estáticas e podem ser regeneradas manualmente**, e os **tokens precisam ser atualizados após a expiração**. Há suporte para a autenticação baseada em chave para a instância de contêiner do Azure e os serviços Web implantados pelo serviço kubernetes do Azure, e a autenticação baseada em token **só** está disponível para implantações do serviço kubernetes do Azure. Para obter mais informações sobre como configurar a autenticação, consulte [Configurar a autenticação para modelos implantados como serviços Web](how-to-authenticate-web-service.md).


#### <a name="authentication-with-keys"></a>Autenticação com chaves

Ao habilitar a autenticação para uma implantação, você cria automaticamente as chaves de autenticação.

* A autenticação é ativada por padrão ao implantar no Serviço de Kubernetes do Azure.
* A autenticação é desabilitada por padrão durante a implantação nas Instâncias de Contêiner do Azure.

Para controlar a autenticação, use o parâmetro `auth_enabled` ao criar ou atualizar uma implantação.

Se a autenticação estiver ativada, você poderá usar o método `get_keys` para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisar regenerar uma chave, use [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) .

#### <a name="authentication-with-tokens"></a>Autenticação com tokens

Quando você habilita a autenticação de token para um serviço Web, um usuário deve fornecer um Azure Machine Learning token JWT para o serviço Web para acessá-lo. 

* A autenticação de token é desabilitada por padrão quando você está implantando no serviço kubernetes do Azure.
* Não há suporte para autenticação de token quando você está implantando em instâncias de contêiner do Azure.

Para controlar a autenticação de token, use o `token_auth_enabled` parâmetro ao criar ou atualizar uma implantação.

Se a autenticação de token estiver habilitada, você poderá usar o `get_token` método para recuperar um token de portador e o tempo de expiração dos tokens:

```python
token, refresh_by = service.get_token()
print(token)
```

Se você tiver o [CLI do Azure e a extensão de Machine Learning](reference-azure-machine-learning-cli.md), poderá usar o seguinte comando para obter um token:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Atualmente, a única maneira de recuperar o token é usando o SDK do Azure Machine Learning ou a extensão de Machine Learning do CLI do Azure.

Será necessário solicitar um novo token após a hora do token `refresh_by` . 

## <a name="request-data"></a>Dados de solicitação

A API REST espera que o corpo da solicitação seja um documento JSON com a seguinte estrutura:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> A estrutura dos dados precisa corresponder ao esperado pelo script e modelo de pontuação no serviço. O script de pontuação pode modificar os dados antes de transmiti-lo ao modelo.

### <a name="binary-data"></a>Dados binários

Para obter informações sobre como habilitar o suporte para dados binários em seu serviço, consulte [dados binários](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> A habilitação do suporte para dados binários ocorre no arquivo score.py usado pelo modelo implantado. No cliente, use a funcionalidade HTTP da sua linguagem de programação. Por exemplo, o trecho a seguir envia o conteúdo de um arquivo JPG para um serviço Web:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>CORS (Compartilhamento de Recursos entre Origens)

Para obter informações sobre como habilitar o suporte a CORS em seu serviço, consulte [compartilhamento de recursos entre origens](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>Chamar o serviço (C#)

Este exemplo demonstra como usar o C # para chamar o serviço da Web criado a partir do exemplo [Treinar no caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Chamar o serviço (Ir)

Este exemplo demonstra como usar o recurso Go para chamar o serviço da Web criado a partir do exemplo [Train dentro do notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Ligue para o serviço (Java)

Este exemplo demonstra como usar o Java para chamar o serviço da Web criado a partir do exemplo [Trainar no notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Chamar o serviço (Python)

Este exemplo demonstra como usar o Python para chamar o serviço da Web criado a partir do exemplo [Traino no notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Esquema de serviço Web (especificação OpenAPI)

Se você usou a geração de esquema automática com sua implantação, você pode obter o endereço da especificação OpenAPI para o serviço usando a [propriedade swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri). (Por exemplo, `print(service.swagger_uri)` .) Use uma solicitação GET ou abra o URI em um navegador para recuperar a especificação.

O documento JSON a seguir é um exemplo de um esquema (especificação de OpenAPI) gerado para uma implantação:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Para obter mais informações, consulte [especificação de openapi](https://swagger.io/specification/).

Para um utilitário que pode criar bibliotecas de cliente a partir da especificação, consulte [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> Você pode recuperar o documento JSON do esquema depois de implantar o serviço. Use a [propriedade swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) do serviço Web implantado (por exemplo, `service.swagger_uri` ) para obter o URI para o arquivo Swagger do serviço Web local.

## <a name="consume-the-service-from-power-bi"></a>Consumir o serviço de Power BI

O Power BI dá suporte ao consumo de serviços Web Azure Machine Learning para enriquecer os dados em Power BI com previsões. 

Para gerar um serviço Web com suporte para consumo no Power BI, o esquema deve dar suporte ao formato exigido pelo Power BI. [Saiba como criar um esquema com suporte a Power bi](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint).

Depois que o serviço Web for implantado, ele será consumível pelos fluxos de dados do Power BI. [Saiba como consumir um serviço Web do Machine Learning do Azure no Power BI](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Próximas etapas

Para exibir uma arquitetura de referência para a pontuação em tempo real dos modelos de aprendizado profundo e Python, vá para o [centro de arquitetura do Azure](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
