---
title: Usar o Azure Webhooks para monitorar as notificações de trabalho dos Serviços de Mídia com o .NET | Microsoft Docs
description: Saiba como usar o Azure Webhooks para monitorar as notificações de trabalho dos Serviços de Mídia. O exemplo de código é escritos em C# e usam o SDK dos Serviços de Mídia para .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a61fe157-81b1-45c1-89f2-224b7ef55869
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 36ef27dfb4a5d77ec2e595013a82f55cdf240c0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465687"
---
# <a name="use-azure-webhooks-to-monitor-media-services-job-notifications-with-net"></a>Usar o Azure Webhooks para monitorar as notificações de trabalho dos Serviços de Mídia com o .NET 

Quando você executa trabalhos, geralmente precisa de uma maneira de acompanhar o andamento do trabalho. Você pode monitorar as notificações de trabalho dos Serviços de Mídia usando o Azure WebHooks ou o [Armazenamento de Filas do Azure](media-services-dotnet-check-job-progress-with-queues.md). Este artigo mostra como trabalhar com webhooks.

Este artigo mostra como

*  Definir uma Azure Function personalizada para responder a webhooks. 
    
    Nesse caso, o webhook é disparado pelos Serviços de Mídia quando o trabalho de codificação altera o status. A função escuta em busca do retorno de chamada do webhook das notificações de Serviços de Mídia e publica o ativo de saída após o trabalho ser concluído. 
    
    >[!NOTE]
    >Antes de continuar, verifique se você entende como [associações HTTP de Azure Functions e webhook](../../azure-functions/functions-bindings-http-webhook.md) funcionam.
    >
    
* Adicione um webhook à sua tarefa de codificação e especifique a URL de webhook e a chave secreta à qual esse webhook responde. Você encontrará um exemplo que adiciona um webhook à tarefa de codificação ao final do artigo.  

Encontre definições de várias Azure Functions do .NET dos Serviços de Mídia (incluindo a mostrada neste artigo) [aqui](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir o tutorial:

* Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, consulte [Como criar uma conta de Serviços de Mídia](media-services-portal-create-account.md).
* Noções básicas de [como usar o Azure Functions](../../azure-functions/functions-overview.md). Além disso, examine [Associações HTTP e de webhook do Azure Functions](../../azure-functions/functions-bindings-http-webhook.md).

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

1. Vá para o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.
2. Crie um aplicativo de função conforme descrito [aqui](../../azure-functions/functions-create-function-app-portal.md).

## <a name="configure-function-app-settings"></a>Definir configurações do aplicativo de funções

Ao desenvolver funções dos Serviços de Mídia, é útil adicionar variáveis de ambiente que serão usadas em todas as funções. Para definir as configurações de aplicativo, clique no link Definir configurações de aplicativo. 

A seção [configurações de aplicativo](media-services-dotnet-how-to-use-azure-functions.md#configure-function-app-settings) define os parâmetros usados no webhook definido neste artigo. Também adicione os parâmetros a seguir às configurações de aplicativo. 

|NOME|Definição|Exemplo| 
|---|---|---|
|SigningKey |Uma chave de assinatura.| j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt|
|WebHookEndpoint | Um endereço do ponto de extremidade do webhook. Depois que a função de webhook for criada, copie a URL do link **Obter URL de função**. | https:\//juliakofuncapp.azurewebsites.net/api/Notification_Webhook_Function?code=iN2phdrTnCxmvaKExFWOTulfnm4C71mMLIy8tzLr7Zvf6Z22HHIK5g==.|

## <a name="create-a-function"></a>Criar uma função

Depois que o aplicativo de funções for implantado, você poderá encontrá-lo entre os **Serviços de Aplicativos** do Azure Functions.

1. Selecione seu aplicativo de funções e clique em **Nova Função**.
2. Selecione o código **C#** e o cenário **API e Webhooks**. 
3. Selecione **Webhook Genérico – C#**.
4. Nomeie o webhook e pressione **Criar**.

### <a name="files"></a>Arquivos

A Azure Function está associada a arquivos de código e a outros arquivos descritos nesta seção. Por padrão, uma função está associada aos arquivos **function.json** e **run.csx** (C#). Você precisa adicionar um arquivo **project.json**. O restante desta seção mostra as definições para esses arquivos.

![de entrada](./media/media-services-azure-functions/media-services-azure-functions003.png)

#### <a name="functionjson"></a>function.json

O arquivo function.json define as associações de função e outras definições de configuração. O tempo de execução usa esse arquivo para determinar os eventos a serem monitorados, bem como para passar e retornar dados da execução da função. 

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

#### <a name="projectjson"></a>project.json

O arquivo project.json contém dependências. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}
```
    
#### <a name="runcsx"></a>run.csx

O código desta seção mostra uma implementação de uma Azure Function que é um webhook. Nesta amostra, a função escuta em busca do retorno de chamada do webhook das notificações de Serviços de Mídia e publica o ativo de saída após o trabalho ser concluído.

O webhook espera uma chave de autenticação (credencial) para corresponder àquela que você passa ao configurar o ponto de extremidade de notificação. A chave de assinatura é o valor com codificação Base64 de 64 bytes que é usado para proteger seus retornos de chamada de WebHooks de Serviços de Mídia do Azure. 

No código de definição de webhook a seguir, o método **VerifyWebHookRequestSignature** faz a verificação da mensagem de notificação. A finalidade dessa validação é garantir que a mensagem foi enviada pelos Serviços de Mídia do Azure e não foi adulterada. A assinatura é opcional para o Azure Functions, pois ela tem o valor **Code** como um parâmetro de consulta por protocolo TLS. 

>[!NOTE]
>Há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política, se você estiver sempre usando os mesmos dias/permissões de acesso, por exemplo, políticas de localizadores que devem permanecer no local por um longo período (políticas de não carregamento). Para obter mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

```csharp
///////////////////////////////////////////////////
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using Newtonsoft.Json;
using Microsoft.Azure;
using System.Net;
using System.Security.Cryptography;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

internal const string SignatureHeaderKey = "sha256";
internal const string SignatureHeaderValueTemplate = SignatureHeaderKey + "={0}";
static string _webHookEndpoint = Environment.GetEnvironmentVariable("WebHookEndpoint");
static string _signingKey = Environment.GetEnvironmentVariable("SigningKey");

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");

static readonly string _AMSClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _AMSClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static CloudMediaContext _context = null;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
    byte[] requestBody = await taskForRequestBody;

    string jsonContent = await req.Content.ReadAsStringAsync();
    log.Info($"Request Body = {jsonContent}");

    IEnumerable<string> values = null;
    if (req.Headers.TryGetValues("ms-signature", out values))
    {
        byte[] signingKey = Convert.FromBase64String(_signingKey);
        string signatureFromHeader = values.FirstOrDefault();

        if (VerifyWebHookRequestSignature(requestBody, signatureFromHeader, signingKey))
        {
            string requestMessageContents = Encoding.UTF8.GetString(requestBody);

            NotificationMessage msg = JsonConvert.DeserializeObject<NotificationMessage>(requestMessageContents);

            if (VerifyHeaders(req, msg, log))
            { 
                string newJobStateStr = (string)msg.Properties.Where(j => j.Key == "NewState").FirstOrDefault().Value;
                if (newJobStateStr == "Finished")
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                                AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                    if(_context!=null)   
                    {                        
                        string urlForClientStreaming = PublishAndBuildStreamingURLs(msg.Properties["JobId"]);
                        log.Info($"URL to the manifest for client streaming using HLS protocol: {urlForClientStreaming}");
                    }
                }

                return req.CreateResponse(HttpStatusCode.OK, string.Empty);
            }
            else
            {
                log.Info($"VerifyHeaders failed.");
                return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyHeaders failed.");
            }
        }
        else
        {
            log.Info($"VerifyWebHookRequestSignature failed.");
            return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyWebHookRequestSignature failed.");
        }
    }

    return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
}

private static string PublishAndBuildStreamingURLs(String jobID)
{
    IJob job = _context.Jobs.Where(j => j.Id == jobID).FirstOrDefault();
    IAsset asset = job.OutputMediaAssets.FirstOrDefault();

    // Create a 30-day readonly access policy. 
    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
    TimeSpan.FromDays(30),
    AccessPermissions.Read);

    // Create a locator to the streaming content on an origin. 
    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
    policy,
    DateTime.UtcNow.AddMinutes(-5));

    // Get a reference to the streaming manifest file from the  
    // collection of files in the asset. 
    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                EndsWith(".ism")).
                FirstOrDefault();

    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest" +  "(format=m3u8-aapl)";
    return urlForClientStreaming;

}

private static bool VerifyWebHookRequestSignature(byte[] data, string actualValue, byte[] verificationKey)
{
    using (var hasher = new HMACSHA256(verificationKey))
    {
        byte[] sha256 = hasher.ComputeHash(data);
        string expectedValue = string.Format(CultureInfo.InvariantCulture, SignatureHeaderValueTemplate, ToHex(sha256));

        return (0 == String.Compare(actualValue, expectedValue, System.StringComparison.Ordinal));
    }
}

private static bool VerifyHeaders(HttpRequestMessage req, NotificationMessage msg, TraceWriter log)
{
    bool headersVerified = false;

    try
    {
        IEnumerable<string> values = null;
        if (req.Headers.TryGetValues("ms-mediaservices-accountid", out values))
        {
            string accountIdHeader = values.FirstOrDefault();
            string accountIdFromMessage = msg.Properties["AccountId"];

            if (0 == string.Compare(accountIdHeader, accountIdFromMessage, StringComparison.OrdinalIgnoreCase))
            {
                headersVerified = true;
            }
            else
            {
                log.Info($"accountIdHeader={accountIdHeader} does not match accountIdFromMessage={accountIdFromMessage}");
            }
        }
        else
        {
            log.Info($"Header ms-mediaservices-accountid not found.");
        }
    }
    catch (Exception e)
    {
        log.Info($"VerifyHeaders hit exception {e}");
        headersVerified = false;
    }

    return headersVerified;
}

private static readonly char[] HexLookup = new char[] { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };

/// <summary>
/// Converts a <see cref="T:byte[]"/> to a hex-encoded string.
/// </summary>
private static string ToHex(byte[] data)
{
    if (data == null)
    {
        return string.Empty;
    }

    char[] content = new char[data.Length * 2];
    int output = 0;
    byte d;

    for (int input = 0; input < data.Length; input++)
    {
        d = data[input];
        content[output++] = HexLookup[d / 0x10];
        content[output++] = HexLookup[d % 0x10];
    }

    return new string(content);
}

internal enum NotificationEventType
{
    None = 0,
    JobStateChange = 1,
    NotificationEndPointRegistration = 2,
    NotificationEndPointUnregistration = 3,
    TaskStateChange = 4,
    TaskProgress = 5
}

internal sealed class NotificationMessage
{
    public string MessageVersion { get; set; }
    public string ETag { get; set; }
    public NotificationEventType EventType { get; set; }
    public DateTime TimeStamp { get; set; }
    public IDictionary<string, string> Properties { get; set; }
}
```

Salve e execute a função.

### <a name="function-output"></a>Saída da função

Depois que o webhook é disparado, o exemplo acima produz a saída a seguir. Os valores variarão.

    C# HTTP trigger function processed a request. RequestUri=https://juliako001-functions.azurewebsites.net/api/Notification_Webhook_Function?code=9376d69kygoy49oft81nel8frty5cme8hb9xsjslxjhalwhfrqd79awz8ic4ieku74dvkdfgvi
    Request Body = 
    {
      "MessageVersion": "1.1",
      "ETag": "b8977308f48858a8f224708bc963e1a09ff917ce730316b4e7ae9137f78f3b20",
      "EventType": 4,
      "TimeStamp": "2017-02-16T03:59:53.3041122Z",
      "Properties": {
        "JobId": "nb:jid:UUID:badd996c-8d7c-4ae0-9bc1-bd7f1902dbdd",
        "TaskId": "nb:tid:UUID:80e26fb9-ee04-4739-abd8-2555dc24639f",
        "NewState": "Finished",
        "OldState": "Processing",
        "AccountName": "mediapkeewmg5c3peq",
        "AccountId": "301912b0-659e-47e0-9bc4-6973f2be3424",
        "NotificationEndPointId": "nb:nepid:UUID:cb5d707b-4db8-45fe-a558-19f8d3306093"
      }
    }
    
    URL to the manifest for client streaming using HLS protocol: http://mediapkeewmg5c3peq.streaming.mediaservices.windows.net/0ac98077-2b58-4db7-a8da-789a13ac6167/BigBuckBunny.ism/manifest(format=m3u8-aapl)

## <a name="add-a-webhook-to-your-encoding-task"></a>Adicionar um webhook à tarefa de codificação

Nesta seção, é mostrado o código que adiciona uma notificação webhook a uma tarefa. Você também pode adicionar uma notificação de trabalho de nível, o que seria mais útil para um trabalho com tarefas encadeadas.  

1. No Visual Studio, crie um novo aplicativo de console C#. Digite o nome, o Local e o Nome da solução e OK.
2. Use o [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) para instalar os Serviços de Mídia do Azure.
3. Atualize o arquivo App.config com valores apropriados: 
    
   * Informações de conexão dos Serviços de Mídia do Azure, 
   * a URL de webhook que espera receber as notificações, 
   * a chave de assinatura que corresponde à chave que o webhook espera. A chave de assinatura é o valor codificado em Base64 de 64 bytes usado para proteger os retornos de chamada de webhooks dos Serviços de Mídia do Azure. 

     ```xml
           <appSettings>
               <add key="AMSAADTenantDomain" value="domain" />
               <add key="AMSRESTAPIEndpoint" value="endpoint" />

               <add key="AMSClientId" value="clinet id" />
               <add key="AMSClientSecret" value="client secret" />

               <add key="WebhookURL" value="https://yourapp.azurewebsites.net/api/functionname?code=ApiKey" />
               <add key="WebhookSigningKey" value="j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt" />
           </appSettings>
     ```

4. Atualize o arquivo Program.cs com o código a seguir:

    ```csharp
            using System;
            using System.Configuration;
            using System.Linq;
            using Microsoft.WindowsAzure.MediaServices.Client;

            namespace NotificationWebHook
            {
                class Program
                {
                // Read values from the App.config file.
                private static readonly string _AMSAADTenantDomain =
                    ConfigurationManager.AppSettings["AMSAADTenantDomain"];
                private static readonly string _AMSRESTAPIEndpoint =
                    ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];

                private static readonly string _AMSClientId =
                    ConfigurationManager.AppSettings["AMSClientId"];
                private static readonly string _AMSClientSecret =
                    ConfigurationManager.AppSettings["AMSClientSecret"];

                private static readonly string _webHookEndpoint =
                    ConfigurationManager.AppSettings["WebhookURL"];
                private static readonly string _signingKey =
                    ConfigurationManager.AppSettings["WebhookSigningKey"];

                // Field for service context.
                private static CloudMediaContext _context = null;

                static void Main(string[] args)
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);

                    byte[] keyBytes = Convert.FromBase64String(_signingKey);

                    IAsset newAsset = _context.Assets.FirstOrDefault();

                    // Check for existing Notification Endpoint with the name "FunctionWebHook"

                    var existingEndpoint = _context.NotificationEndPoints.Where(e => e.Name == "FunctionWebHook").FirstOrDefault();
                    INotificationEndPoint endpoint = null;

                    if (existingEndpoint != null)
                    {
                    Console.WriteLine("webhook endpoint already exists");
                    endpoint = (INotificationEndPoint)existingEndpoint;
                    }
                    else
                    {
                    endpoint = _context.NotificationEndPoints.Create("FunctionWebHook",
                        NotificationEndPointType.WebHook, _webHookEndpoint, keyBytes);
                    Console.WriteLine("Notification Endpoint Created with Key : {0}", keyBytes.ToString());
                    }

                    // Declare a new encoding job with the Standard encoder
                    IJob job = _context.Jobs.Create("MES Job");

                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    ITask task = job.Tasks.AddNew("My encoding task",
                    processor,
                    "Adaptive Streaming",
                    TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(newAsset);

                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew(newAsset.Name, AssetCreationOptions.None);

                    // Add the WebHook notification to this Task and request all notification state changes.
                    // Note that you can also add a job level notification
                    // which would be more useful for a job with chained tasks.  
                    if (endpoint != null)
                    {
                    task.TaskNotificationSubscriptions.AddNew(NotificationJobState.All, endpoint, true);
                    Console.WriteLine("Created Notification Subscription for endpoint: {0}", _webHookEndpoint);
                    }
                    else
                    {
                    Console.WriteLine("No Notification Endpoint is being used");
                    }

                    job.Submit();

                    Console.WriteLine("Expect WebHook to be triggered for the Job ID: {0}", job.Id);
                    Console.WriteLine("Expect WebHook to be triggered for the Task ID: {0}", task.Id);

                    Console.WriteLine("Job Submitted");

                }
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }
                }
            }
    ```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
