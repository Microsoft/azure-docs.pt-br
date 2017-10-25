---
title: "Associações de Aplicativos Móveis do Azure Functions | Microsoft Docs"
description: "Entenda como usar associações dos Aplicativos Móveis do Azure no Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-mobile-apps-bindings"></a>Associações de Aplicativos Móveis do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar associações dos [Aplicativos Móveis do Azure](../app-service-mobile/app-service-mobile-value-prop.md) no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para os Aplicativos Móveis.

As associações de entrada e saída para os Aplicativos Móveis permitem [ler e gravar em tabelas de dados](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) em seu aplicativo móvel.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Associação de entrada dos Aplicativos Móveis
A associação de entrada dos Aplicativos Móveis carrega um registro de um ponto de extremidade de tabela móvel e o passa para a função. Em uma função do C# ou do F#, todas as alterações feitas no registro são enviadas novamente de forma automática para a tabela quando a função é fechada com êxito.

A entrada dos Aplicativos Móveis de uma função usa o seguinte objeto JSON na matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Observe o seguinte:

* `id` pode ser estático ou se basear no gatilho que invoca a função. Por exemplo, se você usar um [gatilho da fila]() para sua função, o `"id": "{queueTrigger}"` usará o valor de cadeia de caracteres da mensagem da fila como a ID de registro a ser recuperada.
* `connection` deve conter o nome de uma configuração de aplicativo no aplicativo de funções que, por sua vez, contém a URL do aplicativo móvel. A função usa essa URL para construir as operações REST necessárias no aplicativo móvel. Você [cria uma configuração de aplicativo no aplicativo de funções]() que contém a URL do aplicativo móvel (que se parece com `http://<appname>.azurewebsites.net`) e especifica o nome da configuração do aplicativo na propriedade `connection` na associação de entrada. 
* Você precisará especificar `apiKey` se [implementar uma chave de API no back-end do aplicativo móvel do Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) ou se [implementar uma chave de API no back-end do aplicativo móvel do .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fazer isso, você [cria uma configuração de aplicativo no aplicativo de funções]() que contém a chave de API e adiciona a propriedade `apiKey` na associação de entrada ao nome da configuração do aplicativo. 
  
  > [!IMPORTANT]
  > Essa chave de API não deve ser compartilhada com seus clientes de aplicativo móvel. Ela só deve ser distribuída com segurança aos clientes do lado do serviço, como o Azure Functions. 
  > 
  > [!NOTE]
  > O Azure Functions armazena suas informações de conexão e as chaves de API como configurações de aplicativo, para que elas não sejam inseridas no repositório de controle do código-fonte. Isso protege as informações confidenciais.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entrada
Esta seção mostra como usar a associação de entrada dos Aplicativos Móveis no código da função. 

Quando o registro com a tabela e a ID de registro especificadas é encontrado, ele é passado para o parâmetro [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) nomeado (ou, no Node.js, ele é passado para o objeto `context.bindings.<name>`). Quando o registro não é encontrado, o parâmetro é `null`. 

Nas funções do C# ou do F#, todas as alterações feitas no registro de entrada (parâmetro de entrada) são enviadas novamente de forma automática para os Aplicativos Móveis quando a função é fechada com êxito. Nas funções do Node.js, use `context.bindings.<name>` para acessar o registro de entrada. Não é possível modificar um registro no Node.js.

<a name="inputsample"></a>

## <a name="input-sample"></a>Amostra de entrada
Suponha que você tem o seguinte function.json, que recupera um registro de tabela do Aplicativo Móvel com a ID da mensagem do gatilho da fila:

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
],
"disabled": false
}
```

Consulte a amostra específica da linguagem que usa o registro de entrada da associação. As amostras do C# e do F# também modificam a propriedade `text` do registro.

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Exemplo de entrada em C# #

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

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Amostra de entrada no Node.js

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Associação de saída dos Aplicativos Móveis
Use a associação de saída dos Aplicativos Móveis para gravar um novo registro em um ponto de extremidade de tabela dos Aplicativos Móveis.  

A saída dos Aplicativos Móveis para uma função usa o seguinte objeto JSON na matriz `bindings` de function.json:

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Observe o seguinte:

* `connection` deve conter o nome de uma configuração de aplicativo no aplicativo de funções que, por sua vez, contém a URL do aplicativo móvel. A função usa essa URL para construir as operações REST necessárias no aplicativo móvel. Você [cria uma configuração de aplicativo no aplicativo de funções]() que contém a URL do aplicativo móvel (que se parece com `http://<appname>.azurewebsites.net`) e especifica o nome da configuração do aplicativo na propriedade `connection` na associação de entrada. 
* Você precisará especificar `apiKey` se [implementar uma chave de API no back-end do aplicativo móvel do Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) ou se [implementar uma chave de API no back-end do aplicativo móvel do .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fazer isso, você [cria uma configuração de aplicativo no aplicativo de funções]() que contém a chave de API e adiciona a propriedade `apiKey` na associação de entrada ao nome da configuração do aplicativo. 
  
  > [!IMPORTANT]
  > Essa chave de API não deve ser compartilhada com seus clientes de aplicativo móvel. Ela só deve ser distribuída com segurança aos clientes do lado do serviço, como o Azure Functions. 
  > 
  > [!NOTE]
  > O Azure Functions armazena suas informações de conexão e as chaves de API como configurações de aplicativo, para que elas não sejam inseridas no repositório de controle do código-fonte. Isso protege as informações confidenciais.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de saída
Esta seção mostra como usar a associação de saída dos Aplicativos Móveis no código da função. 

Nas funções do C#, use um parâmetro de saída nomeado do tipo `out object` para acessar o registro de saída. Nas funções do Node.js, use `context.bindings.<name>` para acessar o registro de saída.

<a name="outputsample"></a>

## <a name="output-sample"></a>Amostra de saída
Suponha que você tem o seguinte function.json, que define um gatilho da fila e uma saída dos Aplicativos Móveis:

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

Consulte a amostra específica da linguagem que cria um registro no ponto de extremidade de tabela dos Aplicativos Móveis com o conteúdo da mensagem da fila.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Amostra de saída em C# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Amostra de saída no Node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

