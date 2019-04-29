---
title: Associações do SendGrid no Azure Functions
description: Referência de associações do SendGrid no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 663283c4dff16194dea78afbf5dd393f1fa833b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437916"
---
# <a name="azure-functions-sendgrid-bindings"></a>Associações do SendGrid no Azure Functions

Este artigo explica como enviar emails usando as associações de [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) no Azure Functions. O Azure Functions suporta uma associação de saída para o SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações SendGrid são fornecidas no Pacote NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), versão 2.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

As associações SendGrid são fornecidas no Pacote NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), versão 3.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exemplo

Consulte o exemplo específico a um idioma:

* [C#](#c-example)
* [Script do C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>Exemplo de C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que usa um gatilho de fila do Barramento de Serviço e uma associação de saída de SendGrid.

#### <a name="synchronous-c-example"></a>Exemplo de C# síncrono:

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
#### <a name="asynchronous-c-example"></a>Exemplo de C# assíncrono:

```cs
[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Poderá omitir a propriedade `ApiKey` do atributo se você tiver sua chave de API em uma configuração de aplicativo denominada "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>Exemplo 2 de C# script

O exemplo a seguir mostra uma associação de saída de SendGrid em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação.

Aqui estão os dados de associação no arquivo *function.json*:

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

### <a name="java-example"></a>Exemplo do Java

O exemplo a seguir usa o `@SendGridOutput` anotação a [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime) associação de saída enviar um email usando o SendGrid.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo a seguir mostra uma associação de saída de SendGrid em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação.

Aqui estão os dados de associação no arquivo *function.json*:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs).

Para obter informações sobre as propriedades de atributo que você pode configurar, consulte [Configuração](#configuration). Aqui está um exemplo de atributo `SendGrid` em uma assinatura de método:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Para ver um exemplo completo, consulte [Exemplo de C#](#c-example).

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `SendGrid` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo**|| Obrigatório – deve ser definido como `sendGrid`.|
|**direction**|| Obrigatório – deve ser definido como `out`.|
|**name**|| Obrigatório – o nome da variável usado no código da função da solicitação ou do corpo da solicitação. Esse valor é ```$return``` quando há apenas um valor retornado. |
|**apiKey**|**ApiKey**| O nome de uma configuração de aplicativo que contém sua chave de API. Se não configurado, o nome da configuração do aplicativo padrão é "AzureWebJobsSendGridApiKey".|
|**to**|**Para**| o endereço de email do destinatário. |
|**from**|**De**| o endereço de email do remetente. |
|**subject**|**Assunto**| o assunto do email. |
|**text**|**Texto**| o conteúdo do email. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração globais disponíveis para esta associação na versão 2.x. O arquivo host.json de exemplo abaixo contém apenas as configurações da versão 2.x para essa associação. Para obter mais informações sobre as configurações globais na versão 2.x, confira a [referência de host.json para o Azure Functions versão 2.x](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|de|n/d|Endereço de email do remetente em todas as funções.| 


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
