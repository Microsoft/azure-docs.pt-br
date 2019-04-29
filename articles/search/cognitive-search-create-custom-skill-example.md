---
title: 'Exemplo: Criar uma habilidade personalizada no pipeline de pesquisa cognitiva - Azure Search'
description: Demonstra o uso da API de Tradução de Texto na habilidade personalizada mapeada para uma pesquisa cognitiva que indexa o pipeline no Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f3b4e6cd18a362775443bb296560a076aaa1497d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344131"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Exemplo: Criar uma habilidade personalizada usando a API de Tradução de Texto

Neste exemplo, saiba como criar uma habilidade personalizada da API da Web que aceita texto em qualquer idioma e as converte-o para inglês. O exemplo usa uma [Função do Azure](https://azure.microsoft.com/services/functions/) para encapsular a [API de Tradução de Texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) para que ela implemente a interface de habilidade personalizada.

## <a name="prerequisites"></a>Pré-requisitos

+ Leia o artigo sobre [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) se você não estiver familiarizado com a interface de entrada/saída que uma habilidade personalizada deve implementar.

+ [Inscreva-se na API de Tradução de Texto](../cognitive-services/translator/translator-text-how-to-signup.md)e obtenha uma chave de API para utilizá-la.

+ Instale o [Visual Studio 2017 versão 15.5](https://www.visualstudio.com/vs/) ou posterior, incluindo a carga de trabalho de desenvolvimento do Azure.

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Embora este exemplo use uma Função do Azure para hospedar uma API da Web, ela não é necessária.  Desde que você atenda aos [requisitos da interface para uma habilidade cognitiva](cognitive-search-custom-skill-interface.md), a abordagem que você adota é irrelevante. No entanto, o Azure Functions facilita a criação de uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

1. No Visual Studio, selecione **Novo** > **Projeto** no menu Arquivo.

1. Na caixa de diálogo Novo Projeto, selecione **Instalado**, expanda **Visual C#** > **Nuvem**, selecione **Azure Functions**, digite um Nome para seu projeto e selecione **OK**. O nome do aplicativo de funções deve ser válido como um namespace do C# e, portanto, não use outros caracteres não alfanuméricos, hífens ou sublinhados.

1. Selecione **do Azure Functions v2 (.NET Core)**. Você também pode fazer isso com a versão 1, mas o código escrito abaixo é baseado no modelo v2.

1. Selecione o tipo de **Gatilho HTTP**

1. Para a Conta de Armazenamento, você pode selecionar **Nenhum**, pois você não precisa de qualquer armazenamento para esta função.

1. Selecione **OK** para criar o projeto de função e a função de gatilho HTTP.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Modifique o código para chamar o Serviço Cognitivo de Tradução

O Visual Studio cria um projeto com uma classe que contém o código padronizado para o tipo de função escolhida. O atributo *FunctionName* no método define o nome da sua função. O atributo *HttpTrigger* especifica que a função é disparada por uma solicitação HTTP.

Agora, substitua todo o conteúdo do arquivo *Function1.cs* com o código a seguir:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Certifique-se de inserir seu próprio valor de *chave* no método *TranslateText* com base na chave que você obteve ao se inscrever para a API de Tradução de Texto.

Este exemplo é um enriquecedor simples que só funciona em um registro de cada vez. Este fato se torna importante posteriormente, ao definir o tamanho do lote para o conjunto de qualificações.

## <a name="test-the-function-from-visual-studio"></a>Testar a função do Visual Studio

Pressione **F5** para executar os comportamentos do programa e da função de teste. Nesse caso, vamos usar a função abaixo para traduzir um texto em espanhol para inglês. Use o Postman ou o Fiddler para enviar uma chamada como mostrado a seguir:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Corpo da solicitação
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Response
Você deverá ver uma resposta semelhante ao exemplo a seguir:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar a função no Azure

Quando estiver satisfeito com o comportamento da função, você pode publicá-la.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**. Escolha **Criar Novo** > **Publicar**.

1. Se você ainda não conectou o Visual Studio à sua conta do Azure, selecione **Adicionar uma conta...**

1. Siga os prompts na tela. Você será solicitado a especificar a conta do Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que você deseja usar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem e uma conta de armazenamento se ainda não tiver nenhuma dessas opções. Ao terminar, selecione **Criar**

1. Depois que a implantação for concluída, anote a URL do site. Ela é o endereço do seu aplicativo de funções no Azure. 

1. No [portal do Azure](https://portal.azure.com), navegue até o grupo de recursos e procure a função Traduzir publicada. Na seção **Gerenciar**, você deverá ver as chaves do host. Selecione o ícone **Cópia** da chave do host *padrão*.  

## <a name="test-the-function-in-azure"></a>Testar a função no Azure

Agora que você tem a chave de host padrão, teste sua função, da seguinte maneira:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Corpo da solicitação
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

Isso deve gerar um resultado semelhante àquele que você viu anteriormente ao executar a função no ambiente local.

## <a name="connect-to-your-pipeline"></a>Conectar-se ao seu pipeline
Agora que você tem uma nova habilidade personalizada, você pode adicioná-la ao seu conjunto de qualificações. O exemplo a seguir mostra como chamar a habilidade. Como a habilidade não lida com lotes, adicione uma instrução para o tamanho máximo do lote ser apenas ```1``` para enviar documentos um de cada vez.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
Parabéns! Você criou seu primeiro enriquecedor personalizado. Agora você pode seguir o mesmo padrão para adicionar sua própria funcionalidade personalizada. 

+ [Adicionar uma habilidade personalizada a um pipeline de pesquisa cognitiva](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
