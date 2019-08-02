---
title: 'Exemplo: Criando um habilidades cognitivo personalizado com a API de pesquisa de entidade do Bing - Azure Search'
description: Demonstra como usar o serviço de pesquisa de entidade do Bing em uma habilidade personalizada, mapeada para um pipeline de indexação de pesquisa cognitiva no Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672137"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Exemplo: Criar uma habilidade personalizada usando a API de pesquisa de entidade do Bing

Neste exemplo, saiba como criar uma habilidade personalizada de API da web. Essa habilidade aceitará locais, figuras público e as organizações e retornar as descrições para eles. O exemplo usa uma [Azure Function](https://azure.microsoft.com/services/functions/) encapsular os [API de pesquisa de entidade do Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para que ele implementa a interface de habilidades personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

+ Leia sobre [interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md) artigo se você não estiver familiarizado com a interface de entrada/saída uma habilidade personalizada deve implementar.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Instale [Visual Studio de 2019](https://www.visualstudio.com/vs/) ou posterior, incluindo a carga de trabalho de desenvolvimento do Azure.

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Embora este exemplo usa uma função do Azure para hospedar uma API da web, não é necessária.  Desde que você atenda aos [requisitos da interface para uma habilidade cognitiva](cognitive-search-custom-skill-interface.md), a abordagem que você adota é irrelevante. No entanto, o Azure Functions facilita a criação de uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

1. No Visual Studio, selecione **Novo** > **Projeto** no menu Arquivo.

1. Na caixa de diálogo Novo Projeto, selecione **Instalado**, expanda **Visual C#**  > **Nuvem**, selecione **Azure Functions**, digite um Nome para seu projeto e selecione **OK**. O nome do aplicativo de função deve ser válido como um C# namespace, portanto, não use sublinhados, hifens ou quaisquer outros caracteres não alfanuméricos.

1. Selecione **do Azure Functions v2 (.NET Core)** . Você também pode fazer isso com a versão 1, mas o código escrito abaixo é baseado no modelo v2.

1. Selecione o tipo de **Gatilho HTTP**

1. Para a Conta de Armazenamento, você pode selecionar **Nenhum**, pois você não precisa de qualquer armazenamento para esta função.

1. Selecione **OK** para criar o projeto de função e a função de gatilho HTTP.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modificar o código para chamar o serviço de pesquisa de entidade do Bing

O Visual Studio cria um projeto com uma classe que contém o código padronizado para o tipo de função escolhida. O atributo *FunctionName* no método define o nome da sua função. O atributo *HttpTrigger* especifica que a função é disparada por uma solicitação HTTP.

Agora, substitua todo o conteúdo do arquivo *Function1.cs* com o código a seguir:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Certifique-se de inserir seu próprio *chave* o valor de `key` constante com base na chave que você obteve ao se inscrever para a API de pesquisa de entidade do Bing.

Este exemplo inclui todo o código necessário em um único arquivo para sua conveniência. Você pode encontrar uma versão um pouco mais estruturada dessa mesma habilidade em [o repositório de habilidades de energia](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

É claro, você pode renomear o arquivo de `Function1.cs` para `BingEntitySearch.cs`.

## <a name="test-the-function-from-visual-studio"></a>Testar a função do Visual Studio

Pressione **F5** para executar os comportamentos do programa e da função de teste. Nesse caso, vamos usar a função abaixo para pesquisar a duas entidades. Use o Postman ou o Fiddler para enviar uma chamada como mostrado a seguir:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Corpo da solicitação
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
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
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar a função no Azure

Quando estiver satisfeito com o comportamento da função, você pode publicá-lo.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**. Escolha **Criar Novo** > **Publicar**.

1. Se você ainda não conectou o Visual Studio à sua conta do Azure, selecione **Adicionar uma conta...**

1. Siga os prompts na tela. Você será solicitado a especificar um nome exclusivo para seu serviço de aplicativo, a assinatura do Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que você deseja usar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem e uma conta de armazenamento se ainda não tiver nenhuma dessas opções. Ao terminar, selecione **Criar**

1. Após a implantação for concluída, observe a URL do Site. Ela é o endereço do seu aplicativo de funções no Azure. 

1. No [portal do Azure](https://portal.azure.com), navegue até o grupo de recursos e procure o `EntitySearch` função que você publicou. Na seção **Gerenciar**, você deverá ver as chaves do host. Selecione o ícone **Cópia** da chave do host *padrão*.  

## <a name="test-the-function-in-azure"></a>Testar a função no Azure

Agora que você tem a chave de host padrão, teste sua função, da seguinte maneira:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Corpo da solicitação
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Este exemplo deve produzir o mesmo resultado que você viu anteriormente ao executar a função no ambiente local.

## <a name="connect-to-your-pipeline"></a>Conectar-se ao seu pipeline
Agora que você tem uma nova habilidade personalizada, você pode adicioná-la ao seu conjunto de qualificações. O exemplo a seguir mostra como chamar a habilidade para adicionar descrições para as organizações do documento (Isso pode ser estendido para funcionar em locais e as pessoas). Substitua `[your-entity-search-app-name]` com o nome do seu aplicativo.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Aqui, contamos no internos [habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) estejam presentes no conjunto de qualificações e incrementou o documento com a lista de organizações. Para referência, aqui está uma configuração de habilidades de extração de entidade que seria suficiente para gerar os dados que precisamos:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Próximas etapas
Parabéns! Você criou seu primeiro aprimorador personalizado. Agora você pode seguir o mesmo padrão para adicionar sua própria funcionalidade personalizada. 

+ [Adicionar uma habilidade personalizada a um pipeline de pesquisa cognitiva](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de qualificações (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
