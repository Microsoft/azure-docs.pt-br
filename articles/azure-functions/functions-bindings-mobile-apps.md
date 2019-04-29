---
title: Associações de Aplicativos Móveis para o Azure Functions
description: Entenda como usar associações dos Aplicativos Móveis do Azure no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5fd220f15f363c1987f1576009519e4b2feae6b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438118"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Associações de Aplicativos Móveis para o Azure Functions 

> [!NOTE]
> Associações de aplicativos móveis do Azure só estão disponíveis para o Azure Functions 1.x. Eles não têm suporte no Azure Functions 2.x.

Este artigo explica como trabalhar com associações dos [Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-value-prop.md) no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para os Aplicativos Móveis.

As associações dos Aplicativos Móveis permitem ler e atualizar tabelas de dados em aplicativos móveis.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As ligações de aplicativos para dispositivos móveis são fornecidas no pacote [ do Microsoft.Azure.WebJobs.Extensions.MobileApps ](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet, versão 1.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Entrada

A associação de entrada dos Aplicativos Móveis carrega um registro de um ponto de extremidade de tabela móvel e o passa para a função. Em funções do C# ou do F#, todas as alterações feitas no registro são enviadas novamente de forma automática para a tabela quando a função é fechada com êxito.

## <a name="input---example"></a>Entrada - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Entrada - exemplo de script C#

O exemplo a seguir mostra uma associação de entrada dos Aplicativos Móveis em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função é disparada por uma mensagem da fila que possui um identificador de registro. A função lê o registro especificado e modifica sua propriedade `Text`.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Entrada - JavaScript

O exemplo a seguir mostra uma associação de entrada dos Aplicativos Móveis em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função é disparada por uma mensagem da fila que possui um identificador de registro. A função lê o registro especificado e modifica sua propriedade `Text`.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Entrada – atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs).

Para saber informações sobre propriedades de atributo que podem ser configuradas, consulte [a seção de configuração a seguir](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `MobileTable` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
| **tipo**|| Deve ser definido como "mobileTable"|
| **direction**||Deve ser definido como "in"|
| **name**|| Nome do parâmetro de entrada na assinatura de função.|
|**tableName** |**TableName**|Nome da tabela de dados do aplicativo móvel|
| **ID**| **Id** | O identificador exclusivo do registro a ser recuperado. Pode ser estático ou se basear no gatilho que invoca a função. Por exemplo, se você usar um gatilho da fila para sua função, o `"id": "{queueTrigger}"` usará o valor de cadeia de caracteres da mensagem da fila como a ID de registro a ser recuperada.|
|**conexão**|**Conexão**|O nome de uma configuração de aplicativo que tem a URL do aplicativo móvel. A função usa essa URL para construir as operações REST necessárias no aplicativo móvel. Crie uma configuração de aplicativo no aplicativo de funções que contenha a URL do aplicativo móvel e especifique o nome da configuração do aplicativo na propriedade `connection` na associação de entrada. A URL é semelhante a `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|O nome de uma configuração de aplicativo que tem a chave de API do seu aplicativo móvel. Forneça a chave de API se [implementar uma chave de API no aplicativo móvel do Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) ou se [implementar uma chave de API no aplicativo móvel do .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma configuração de aplicativo no aplicativo de funções que contém a chave de API e adicione a propriedade `apiKey` na associação de entrada ao nome da configuração do aplicativo. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não compartilhe a chave de API com seus clientes de aplicativo móvel. Ela só deve ser distribuída com segurança aos clientes do lado do serviço, como o Azure Functions. O Azure Functions armazena suas informações de conexão e as chaves de API como configurações de aplicativo, para que elas não sejam inseridas no repositório de controle do código-fonte. Isso protege as informações confidenciais.

## <a name="input---usage"></a>Entrada - uso

Em funções do C#, quando o registro com a ID especificada for encontrado, ele é passado para o parâmetro chamado [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm). Quando o registro não é encontrado, o valor do parâmetro é `null`. 

Em funções do JavaScript, o registro é passado para o objeto `context.bindings.<name>`. Quando o registro não é encontrado, o valor do parâmetro é `null`. 

Nas funções do C# ou do F#, todas as alterações feitas no registro de entrada (parâmetro de entrada) são enviadas novamente de forma automática para a tabela quando a função é fechada com êxito. Não é possível modificar um registro em funções do JavaScript.

## <a name="output"></a>Saída

Use a associação de saída dos Aplicativos Móveis para gravar um novo registro em um ponto de extremidade da tabela dos Aplicativos Móveis.  

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#output---c-example)
* [Script do C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é disparada por uma mensagem da fila e cria um registro em uma tabela do aplicativo móvel.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

O exemplo a seguir mostra uma associação de saída dos Aplicativos Móveis em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função é disparada por uma mensagem da fila e cria um novo registro com o valor inserido no código para a propriedade `Text`.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

O exemplo a seguir mostra uma associação de saída dos Aplicativos Móveis em um arquivo *function.json* e uma [função do JavaScript](functions-reference-node.md) que usa a associação. A função é disparada por uma mensagem da fila e cria um novo registro com o valor inserido no código para a propriedade `Text`.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs).

Para saber informações sobre propriedades de atributo que podem ser configuradas, consulte [Saída - configuração](#output---configuration). Aqui está um exemplo de atributo `MobileTable` em uma assinatura de método:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output---c-example).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `MobileTable` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
| **tipo**|| Deve ser definido como "mobileTable"|
| **direction**||Deve ser definido como "out"|
| **name**|| Nome do parâmetro de saída na assinatura de função.|
|**tableName** |**TableName**|Nome da tabela de dados do aplicativo móvel|
|**conexão**|**MobileAppUriSetting**|O nome de uma configuração de aplicativo que tem a URL do aplicativo móvel. A função usa essa URL para construir as operações REST necessárias no aplicativo móvel. Crie uma configuração de aplicativo no aplicativo de funções que contenha a URL do aplicativo móvel e especifique o nome da configuração do aplicativo na propriedade `connection` na associação de entrada. A URL é semelhante a `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|O nome de uma configuração de aplicativo que tem a chave de API do seu aplicativo móvel. Forneça a chave de API se [implementar uma chave de API no back-end do aplicativo móvel do Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) ou se [implementar uma chave de API no back-end do aplicativo móvel do .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma configuração de aplicativo no aplicativo de funções que contém a chave de API e adicione a propriedade `apiKey` na associação de entrada ao nome da configuração do aplicativo. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não compartilhe a chave de API com seus clientes de aplicativo móvel. Ela só deve ser distribuída com segurança aos clientes do lado do serviço, como o Azure Functions. O Azure Functions armazena suas informações de conexão e as chaves de API como configurações de aplicativo, para que elas não sejam inseridas no repositório de controle do código-fonte. Isso protege as informações confidenciais.

## <a name="output---usage"></a>Saída - uso

Nas funções do script C#, use um parâmetro de saída nomeado do tipo `out object` para acessar o registro de saída. Em bibliotecas de classes C#, o atributo `MobileTable` pode ser usado com qualquer um dos seguintes tipos:

* `ICollector<T>` ou `IAsyncCollector<T>`, onde `T` é `JObject` ou qualquer tipo com uma propriedade `public string Id`.
* `out JObject`
* `out T` ou `out T[]`, onde `T` é qualquer Tipo com uma propriedade `public string Id`.

Nas funções do Node.js, use `context.bindings.<name>` para acessar o registro de saída.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
