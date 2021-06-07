---
title: 'ML Studio (clássico): consumir serviços Web-Azure'
description: Depois que um serviço de Machine Learning é implantado do Azure Machine Learning Studio (clássico), o serviço Web RESTFul pode ser consumido como serviço de solicitação-resposta em tempo real ou como um serviço de execução em lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 0dc49265c0ea799e194e4ac7004b558d8a9d4dd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519262"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Como consumir um serviço Web Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Esta é uma marca de seleção, o que significa que este artigo se aplica ao Machine Learning Studio (clássico).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![Isto é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Depois de implantar um modelo de previsão de Azure Machine Learning Studio (clássico) como um serviço Web, você pode usar uma API REST para enviar dados de ti e obter previsões. Você pode enviar os dados em tempo real ou em modo de lote.

Você pode encontrar mais informações sobre como criar e implantar um serviço Web Machine Learning usando Machine Learning Studio (clássico) aqui:

* Para obter um tutorial sobre como criar um experimento no Machine Learning Studio (clássico), consulte [criar seu primeiro experimento](create-experiment.md).
* Para obter detalhes sobre como implantar um serviço Web, consulte [implantar um serviço web Machine Learning](deploy-a-machine-learning-web-service.md).
* Para obter mais informações sobre o Machine Learning em geral, visite o [Centro de Documentação do Machine Learning do Azure](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Visão geral
Com o serviço Web do Azure Machine Learning, um aplicativo externo se comunica com um modelo de pontuação do fluxo de trabalho do Machine Learning em tempo real. Uma chamada do serviço Web do Machine Learning retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada de serviço Web do Machine Learning, transmita uma chave de API que é criada quando você implanta uma previsão. O serviço Web do Machine Learning baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

Azure Machine Learning Studio (clássico) tem dois tipos de serviços:

* RRS (serviço de Request-Response) – um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem estado criados e implantados a partir do Machine Learning Studio (clássico).
* Serviço de Execução de Lote (BES) – Um serviço assíncrono que pontua um lote de registros de dados.

Para obter mais informações sobre Machine Learning serviços Web, consulte [implantar um serviço web Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Obter uma chave de autorização
Quando você implanta seu experimento, as chaves de API são geradas para o serviço Web. Você pode recuperar as chaves de vários locais.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Por meio do portal de serviços Web do Microsoft Azure Machine Learning
Entre no portal de [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net) .

Para recuperar a chave de API para um novo serviço Web do Machine Learning:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.
3. No menu superior, clique em **Consumir**.
4. Copie e salve a **Chave Primária**.

Para recuperar a chave de API para um serviço Web clássico do Machine Learning:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web Clássicos** no menu superior.
2. Clique no serviço Web com o qual está trabalhando.
3. Clique no ponto de extremidade para o qual deseja recuperar a chave.
4. No menu superior, clique em **Consumir**.
5. Copie e salve a **Chave Primária**.

### <a name="classic-web-service"></a>Serviço Web Clássico
 Você também pode recuperar uma chave para um serviço Web clássico do Machine Learning Studio (clássico).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (clássico)
1. Em Machine Learning Studio (clássico), clique em **Serviços Web** à esquerda.
2. Clique em um serviço Web. A **Chave de API** está na guia **PAINEL**.

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Conectar-se a um serviço Web Machine Learning
Você pode se conectar a um serviço Web do Machine Learning usando qualquer linguagem de programação que dá suporte à resposta e à solicitação HTTP. Você pode exibir exemplos em C#, Python e R de uma página de ajuda do serviço Web do Machine Learning.

**Ajuda da API Machine Learning** Machine Learning a ajuda da API é criada quando você implanta um serviço Web. Consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
A ajuda da API do Machine Learning contém detalhes sobre um serviço Web de previsão.

1. Clique no serviço Web com o qual está trabalhando.
2. Clique no ponto de extremidade para o qual deseja exibir uma página de ajuda da API.
3. No menu superior, clique em **Consumir**.
4. Clique na **página de ajuda da API** nos pontos de extremidade Solicitação de Resposta ou Execução em Lote.

**Como exibir a ajuda da API do Machine Learning para um novo serviço Web**

No [portal de serviços Web do Azure Machine Learning](https://services.azureml.net/):

1. Clique em **SERVIÇOS WEB** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.

Clique em **Usar Serviço Web** para obter os URIs dos Serviços de Solicitação-Resposta e Execução em Lotes, bem como o código de exemplo, em C#, R e Python.

Clique em **API do Swagger** para obter a documentação baseada no Swagger para as APIs chamadas dos URIs fornecidos.

### <a name="c-sample"></a>Exemplo de C#
Para se conectar a um serviço Web Machine Learning, use um **HttpClient** passando ScoreData. ScoreData contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Machine Learning com uma chave de API.

Para se conectar a um serviço Web Machine Learning, o pacote NuGet **Microsoft. AspNet. WebApi. Client** deve ser instalado.

**Instalar o NuGet Microsoft. AspNet. WebApi. Client no Visual Studio**

1. Publique “Baixe o conjunto de dados de Download de UCI: Serviço Web do conjunto de dados da classe Adulto 2”.
2. Clique em **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador de pacotes**.
3. Escolha **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para executar o exemplo de código**

1. Publique o experimento “Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2”, parte da coleção de exemplos de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte **obter uma chave de autorização** acima.
3. Atribua serviceUri com o URI de solicitação.

**Esta é a aparência de uma solicitação concluída.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Exemplo de Python
Para conectar-se a um serviço Web de Machine Learning, use a biblioteca **urllib2** do Python 2. x e a biblioteca **urllib.request** do Python 3. x. Você passará o ScoreData, que contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Machine Learning com uma chave de API.

**Para executar o exemplo de código**

1. Implante o experimento "Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2", parte da coleção de exemplos de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte a seção **obter uma chave de autorização** próximo ao início deste artigo.
3. Atribua serviceUri com o URI de solicitação.

**Esta é a aparência de uma solicitação concluída.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Exemplo de R

Para se conectar a um serviço Web de Machine Learning, use as bibliotecas **RCurl** e **rjson** para fazer a solicitação e processar a resposta JSON retornada. Você passará o ScoreData, que contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Machine Learning com uma chave de API.

**Esta é a aparência de uma solicitação concluída.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>Exemplo de JavaScript

Para se conectar a um serviço Web de Machine Learning, use o pacote npm de **solicitação** em seu projeto. Você também usará o objeto `JSON` para formatar sua entrada e analisar o resultado. Instale usando `npm install request --save` ou adicione `"request": "*"` ao package.json em `dependencies` e execute `npm install`.

**Esta é a aparência de uma solicitação concluída.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```