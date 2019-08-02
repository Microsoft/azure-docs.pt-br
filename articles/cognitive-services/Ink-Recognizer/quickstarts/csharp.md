---
title: 'Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o C#'
titleSuffix: Azure Cognitive Services
description: Use a API de Reconhecimento de Tinta Digital para começar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6a1b4ab43a7d87ac1162a7f0a3556d6bc3bfbfab
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721253"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o C#

Use este início rápido para começar a enviar traços de tinta digital para a API de Reconhecimento de Tinta Digital. Este aplicativo C# envia uma solicitação de API que contém dados de traço de tinta formatados em JSON e obtém a resposta.

Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamará a API em um aplicativo de escrita à tinta digital. Este início rápido envia dados de traço de tinta para a amostra manuscrita a seguir de um arquivo JSON.

![uma imagem de um texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar o Newtonsoft.Json como um pacote NuGet no Visual Studio:
        1. Clique com o botão direito do mouse no **Gerenciador de Soluções**
        2. Clique em **Gerenciar Pacotes NuGet...**
        3. Pesquise `Newtonsoft.Json` e instale o pacote
- Se você estiver usando o Linux/macOS, esse aplicativo poderá ser executado com o [Mono](https://www.mono-project.com/).

- Os dados de traço de tinta de exemplo deste início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. No Visual Studio, crie uma solução de console e adicione os pacotes a seguir. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Crie variáveis para a chave de assinatura e o ponto de extremidade. Veja abaixo o URI que você pode usar para o reconhecimento de tinta. Ele será acrescentado ao ponto de extremidade de serviço posteriormente para criar a URL de solicitação da API.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma função assíncrona chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de cabeçalho usando um objeto `HttpClient`. Adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, crie um objeto `StringContent` para a solicitação.
 
3. Envie a solicitação com `PutAsync()`. Se a solicitação for bem-sucedida, retorne a resposta.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

1. Crie uma função chamada `recognizeInk()`. Construa a solicitação e envie-a chamando a função `Request()` com o ponto de extremidade, a chave de assinatura, a URL para a API e os dados de traço de tinta digital.

2. Desserialize o objeto JSON e grave-o no console. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Carregar os dados de tinta digital

Crie uma função chamada `LoadJson()` para carregar o arquivo JSON de dados de tinta. Use um `StreamReader` e um `JsonTextReader` para criar um `JObject` e retorne-o.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Enviar a solicitação de API

1. No método principal do aplicativo, carregue os dados JSON com a função criada acima. 

2. Chame a função `recognizeInk()` criada acima. Use `System.Console.ReadKey()` para manter a janela do console aberta depois de executar o aplicativo.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

Execute o aplicativo. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API do Reconhecimento de Tinta Digital funciona em um aplicativo de escrita à tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C# e UWP (Plataforma Universal do Windows)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da Web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
