---
title: "Associações do Microsoft Graph do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações do Microsoft Graph no Azure Functions."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.openlocfilehash: 8cf2e4e9e9007549dbdc931b4485c4230c536479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-microsoft-graph-bindings"></a>Associações do Microsoft Graph do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com gatilhos e associações do Microsoft Graph no Azure Functions.
Com isso, você pode usar o Azure Functions para trabalhar com eventos, percepções e dados do [Microsoft Graph](https://graph.microsoft.io).

A extensão do Microsoft Graph oferece as seguintes associações:
- Um [associação de entrada do token de autenticação](#token-input) permite que você interaja com qualquer API do Microsoft Graph.
- Uma [associação de entrada de tabela do Excel](#excel-input) permite que você leia os dados do Excel.
- Uma [associação de saída de tabela do Excel](#excel-output) permite que você modifique os dados do Excel.
- Uma [associação de entrada de arquivo do OneDrive](#onedrive-input) permite que você leia arquivos do OneDrive.
- Uma [associação de saída de arquivo do OneDrive](#onedrive-output) permite que você grave arquivos no OneDrive.
- Uma [associação de saída de mensagem do Outlook](#outlook-output) permite que você envie email por meio do Outlook.
- Uma coleção de [gatilhos e associações do Microsoft Graph webhook](#webhooks) permite reagir a eventos do Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Associações do Microsoft Graph estão em versão prévia no momento.

## <a name="setting-up-the-extensions"></a>Como configurar as extensões

As associações do Microsoft Graph estão disponíveis por meio de _extensões de associação_. Extensões de associação são componentes opcionais para o tempo de execução do Azure Functions. Esta seção mostrará como configurar o Microsoft Graph e as extensões de token de autenticação.

### <a name="enabling-functions-20-preview"></a>Habilitar versão prévia do Functions 2.0

Extensões de associação só estão disponíveis para versão prévia do Azure Functions 2.0. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Para obter mais informações, consulte [Como direcionar para versões de tempo de execução do Azure Functions](functions-versions.md).

### <a name="installing-the-extension"></a>Como instalar a extensão

Para instalar uma extensão do portal do Azure, você precisará navegar até um modelo ou associação que faça referência a ela. Crie uma nova função e, enquanto estiver na tela de seleção de modelo, escolha o cenário "Microsoft Graph". Selecione um dos modelos desse cenário. Como alternativa, você pode navegar até a guia "Integração" de uma função existente e selecionar uma das associações abordadas neste tópico.

Em ambos os casos, um aviso será exibido especificando a extensão a ser instalada. Clique em **Instalar** para obter a extensão.

> [!Note] 
> Cada extensão precisa ser instalada apenas uma vez por aplicativo de funções. O processo de instalação no portal pode levar até 10 minutos em um plano de consumo.

Se você estiver usando o Visual Studio, poderá obter as extensões instalando estes pacotes do NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>Como configurar a autenticação/autorização do Serviço de Aplicativo

As associações descritas neste tópico exigem que uma identidade seja usada. Isso permite que o Microsoft Graph aplique permissões e interações de auditoria. A identidade pode ser um usuário acessando seu aplicativo ou o aplicativo em si. Para configurar essa identidade, você precisará configurar [Autenticação/autorização de Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) com o Azure Active Directory. Você também precisará solicitar quaisquer permissões de recurso que suas funções exijam.

> [!Note] 
> A extensão Microsoft Graph tem suporte apenas para autenticação do AAD. Os usuários precisam fazer logon com uma conta corporativa ou de estudante.

Se você estiver usando o portal do Azure, abaixo de solicitação para instalar a extensão, você verá um aviso que solicita que você configure a Autenticação/Autorização do Serviço de Aplicativo e solicita quaisquer permissões de que o modelo ou associação precise. Clique em **Configurar AAD agora** ou **Adicionar permissões agora** conforme apropriado.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Associação de entrada de token de autenticação

Essa associação obtém um token do AAD para um determinado recurso e fornece-o ao seu código como uma cadeia de caracteres. O recurso pode ser qualquer um para o qual o aplicativo tenha permissões. 

### <a name="configuring-an-auth-token-input-binding"></a>Como configurar uma associação de entrada do token de autenticação

A associação em si não requer permissões do AAD, mas, dependendo de como o token é usado, talvez seja necessário solicitar permissões adicionais. Verifique os requisitos do recurso que você pretende acessar com o token.

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatório – o nome da variável usada no código de função para o token de autenticação. Consulte [Como usar uma associação de entrada do token de autenticação do código](#token-input-code).|
|**tipo**|Obrigatório – deve ser definido como `token`.|
|**direction**|Obrigatório – deve ser definido como `in`.|
|**identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**recurso**|Obrigatório – uma URL de recurso do AAD para a qual o token está sendo solicitado.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Como usar uma associação de entrada do token de autenticação do código

O token sempre é apresentado ao código como uma cadeia de caracteres.

#### <a name="sample-getting-user-profile-information"></a>Exemplo: como obter informações de perfil do usuário

Suponha que você tenha o seguinte function.json que defina um gatilho HTTP com uma associação de entrada do token:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir usa o token para fazer uma chamada HTTP para o Microsoft Graph e retorna o resultado:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

O exemplo de JS a seguir usa o token para fazer uma chamada HTTP para o Microsoft Graph e retorna o resultado. Em `function.json` acima, altere `$return` para `res` primeiro.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Associação de entrada da tabela do Excel

Essa associação lê o conteúdo de uma tabela do Excel armazenada no OneDrive.

### <a name="configuring-an-excel-table-input-binding"></a>Como configurar uma associação de entrada de tabela do Excel

Esta associação exige as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler arquivos de usuário|

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatório – o nome da variável usada no código de função para a tabela do Excel. Consulte [Como usar uma associação de entrada de tabela do Excel do código](#excel-input-code).|
|**tipo**|Obrigatório – deve ser definido como `excel`.|
|**direction**|Obrigatório – deve ser definido como `in`.|
|**identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|Obrigatório – o caminho no OneDrive para a pasta de trabalho do Excel.|
|**worksheetName**|A planilha em que a tabela é encontrada.|
|**tableName**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Usando uma associação de entrada de tabela do Excel do código

A associação expõe os seguintes tipos a funções .NET:
- string[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (usando a associação de modelos estrutural)

#### <a name="sample-reading-an-excel-table"></a>Exemplo: leitura de uma tabela do Excel

Suponha que você tenha o seguinte function.json, que define um gatilho HTTP com uma associação de entrada do Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir adiciona e lê o conteúdo da tabela especificada e retorna-o ao usuário:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

O exemplo de JS seguir adiciona e lê o conteúdo da tabela especificada e retorna-o ao usuário. Em `function.json` acima, altere `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Associação de saída da tabela do Excel

Essa associação modifica o conteúdo de uma tabela do Excel armazenada no OneDrive.

### <a name="configuring-an-excel-table-output-binding"></a>Como configurar uma associação de saída de tabela do Excel

Esta associação exige as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso completo aos arquivos do usuário|

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatório – o nome da variável usada no código de função para o token de autenticação. Consulte [Como usar uma associação de saída de tabela do Excel do código](#excel-output-code).|
|**tipo**|Obrigatório – deve ser definido como `excel`.|
|**direction**|Obrigatório – deve ser definido como `out`.|
|**identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|Obrigatório – o caminho no OneDrive para a pasta de trabalho do Excel.|
|**worksheetName**|A planilha em que a tabela é encontrada.|
|**tableName**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|
|**updateType**|Obrigatório – o tipo de alteração a fazer na tabela. Pode ser um dos seguintes valores:<ul><li><code>update</code> – substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code> – adiciona a carga no final da tabela no OneDrive criando novas linhas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Como usar uma associação de saída de tabela do Excel do código

A associação expõe os seguintes tipos a funções .NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (usando a associação de modelos estrutural)

#### <a name="sample-adding-rows-to-an-excel-table"></a>Exemplo: adicionar linhas a uma tabela do Excel

Suponha que você tenha o seguinte function.json que defina um gatilho HTTP com uma associação de entrada do Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


O exemplo de C# a seguir adiciona uma nova linha na tabela (devem para ter uma única coluna) com base na entrada da cadeia de caracteres de consulta:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

O exemplo de JS a seguir adiciona uma nova linha à tabela (presumidamente de uma única coluna) com base na entrada da cadeia de caracteres de consulta. Em `function.json` acima, altere `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>Associação de entrada de arquivo do OneDrive

Essa associação lê o conteúdo de um arquivo armazenado no OneDrive.

### <a name="configuring-a-onedrive-file-input-binding"></a>Como configurar uma associação de entrada de arquivo do OneDrive

Esta associação exige as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler arquivos de usuário|

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatório – o nome da variável usada no código de função para o arquivo. Consulte [Como usar uma associação de entrada de arquivo do OneDrive do código](#onedrive-input-code).|
|**tipo**|Obrigatório – deve ser definido como `onedrive`.|
|**direction**|Obrigatório – deve ser definido como `in`.|
|**identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|Obrigatória – o caminho no OneDrive para o arquivo.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>Como usar uma associação de entrada de arquivo do OneDrive do código

A associação expõe os seguintes tipos a funções .NET:
- byte[]
- Fluxo
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>Exemplo: leitura de um arquivo do OneDrive

Suponha que você tenha o seguinte function.json que defina um gatilho HTTP com uma associação de entrada do OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir lê o arquivo especificado na cadeia de caracteres de consulta e registra seu comprimento:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

O exemplo de JS a seguir lê o arquivo especificado na cadeia de caracteres de consulta e registra seu comprimento. Em `function.json` acima, altere `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>Associação de saída de arquivo do OneDrive

Essa associação modifica o conteúdo de um arquivo armazenado no OneDrive.

### <a name="configuring-a-onedrive-file-output-binding"></a>Como configurar uma associação de saída de arquivo do OneDrive

Esta associação exige as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso completo aos arquivos do usuário|

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatório – o nome da variável usada no código de função para o arquivo. Consulte [Como usar uma associação de saída de arquivo do OneDrive do código](#onedrive-output-code).|
|**tipo**|Obrigatório – deve ser definido como `onedrive`.|
|**direction**|Obrigatório – deve ser definido como `out`.|
|**identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|Obrigatória – o caminho no OneDrive para o arquivo.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>Como usar uma associação de saída de arquivo do OneDrive do código

A associação expõe os seguintes tipos a funções .NET:
- byte[]
- Fluxo
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Exemplo: gravando um arquivo no OneDrive

Suponha que você tenha o seguinte function.json que defina um gatilho HTTP com uma associação de saída do OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir obtém o texto da cadeia de caracteres de consulta e grava-o em um arquivo de texto (FunctionsTest.txt conforme definido na configuração acima) na raiz do OneDrive do chamador:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
O exemplo de JS # a seguir obtém o texto da cadeia de caracteres de consulta e grava-o em um arquivo de texto (FunctionsTest.txt conforme definido na configuração acima) na raiz do OneDrive do chamador. Em `function.json` acima, altere `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Associação de saída de mensagem do Outlook

Envia uma mensagem de email pelo Outlook.

### <a name="configuring-an-outlook-message-output-binding"></a>Como configurar uma associação de saída de mensagem do Outlook

Esta associação exige as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Enviar email como usuário|

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**tipo**|Obrigatório – deve ser definido como `outlook`.|
|**direction**|Obrigatório – deve ser definido como `out`.|
|**identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Como usar uma associação de saída da mensagem do Outlook do código

A associação expõe os seguintes tipos a funções .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Tipos de objetos personalizados (usando a associação de modelos estrutural)

#### <a name="sample-sending-an-email-through-outlook"></a>Exemplo: como enviar um email pelo Outlook

Suponha que você tenha o seguinte function.json que defina um gatilho HTTP com uma associação de saída de mensagem do Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir envia um email do chamador para um destinatário especificado na cadeia de caracteres de consulta:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

O exemplo de JS a seguir envia um email do chamador para um destinatário especificado na cadeia de caracteres de consulta:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Associações de webhook do Microsoft Graph

Webhooks permitem reagir a eventos no Microsoft Graph. Para dar suporte a webhooks, são necessárias funções para criar, atualizar e reagir a _assinaturas de webhook_. Uma solução completa de webhook exigirá uma combinação das seguintes associações:
- Um [gatilho de webhook do Microsoft Graph](#webhook-trigger) permite reagir a um webhook de entrada.
- Uma [associação de entrada de assinatura do webhook do Microsoft Graph](#webhook-input) permite que você liste as assinaturas existentes e, opcionalmente, atualize-as.
- Uma [associação de saída de assinatura de webhook do Microsoft Graph](#webhook-output) permite que você crie ou exclua assinaturas do webhook.

As associações em si não exigem permissões do AAD, mas você precisará solicitar permissões relevantes para o tipo de recurso ao qual você deseja reagir. Para obter uma lista de quais permissões são necessárias para cada tipo de recurso, consulte [permissões de assinatura](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Para obter mais informações sobre webhooks, consulte [Como trabalhar com webhooks no Microsoft Graph].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Gatilho de webhook do Microsoft Graph

Esse gatilho permite que uma função reaja a um webhook de entrada do Microsoft Graph. Cada instância deste gatilho pode reagir a um tipo de recurso do Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Como configurar um gatilho de webhook do Microsoft Graph

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**tipo**|Obrigatório – deve ser definido como `graphWebhook`.|
|**direction**|Obrigatório – deve ser definido como `trigger`.|
|**resourceType**|Obrigatório – o recurso de gráfico para o qual essa função deve responder a webhooks. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code> – alterações feitas em mensagens do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> – alterações feitas aos itens de raiz do OneDrive.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> Um aplicativo de função só pode ter uma função que esteja registrada em relação a um determinado valor de `resourceType`.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>Como configurar um gatilho de webhook do Microsoft Graph do código

A associação expõe os seguintes tipos a funções .NET:
- Tipos de SDK do Microsoft Graph relevante ao tipo de recurso, por exemplo, Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- Tipos de objetos personalizados (usando a associação de modelos estrutural)

Para obter exemplos de como usar essa associação no código, consulte [exemplos do Microsoft Graph webhook](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Associação de entrada de assinatura do webhook do Microsoft Graph

Essa associação permite que você recupere a lista de assinaturas gerenciadas por este aplicativo de função. A associação lê do armazenamento do aplicativo de função e não reflete outras assinaturas criadas de fora do aplicativo.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Como configurar uma associação de entrada de assinatura do webhook do Microsoft Graph

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**tipo**|Obrigatório – deve ser definido como `graphWebhookSubscription`.|
|**direction**|Obrigatório – deve ser definido como `in`.|
|**filter**| Se definido como `userFromRequest`, a associação apenas recuperará assinaturas pertencentes ao usuário que está chamando (válido somente com [gatilho HTTP]).| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Como usar uma associação de entrada de assinatura do webhook do Microsoft Graph do código

A associação expõe os seguintes tipos a funções .NET:
- string[]
- Matrizes de tipo de objeto personalizado
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

Para obter exemplos de como usar essa associação no código, consulte [exemplos do Microsoft Graph webhook](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Associação de saída de assinatura do webhook do Microsoft Graph

Essa associação permite que você crie, exclua e atualize as assinaturas de webhook no Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Como configurar uma associação de saída de assinatura de webhook do Microsoft Graph

A associação dá suporte às seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**name**|Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**tipo**|Obrigatório – deve ser definido como `graphWebhookSubscription`.|
|**direction**|Obrigatório – deve ser definido como `out`.|
|**identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**action**|Obrigatório – especifica a ação que a associação deve executar. Pode ser um dos seguintes valores:<ul><li><code>create</code> – registrar uma nova assinatura.</li><li><code>delete</code> – exclui uma assinatura especificada.</li><li><code>refresh</code> – atualiza uma assinatura especificada para impedir que ela expire.</li></ul>|
|**subscriptionResource**|Necessário se e somente se a _ação_ estiver definida como `create`. Especifica o recurso Microsoft Graph que será monitorado quanto a alterações. Consulte [Como trabalhar com webhooks no Microsoft Graph]. |
|**changeType**|Necessário se e somente se a _ação_ estiver definida como `create`. Indica o tipo de alteração no recurso assinado que gerará uma notificação. Os valores com suporte são: `created`, `updated`, `deleted`. Você pode combinar diversos valores usando uma lista separada por vírgulas.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>Como usar uma associação de saída de assinatura do webhook do Microsoft Graph do código

A associação expõe os seguintes tipos a funções .NET:
- string
- Microsoft.Graph.Subscription

Para obter exemplos de como usar essa associação no código, consulte [exemplos do Microsoft Graph webhook](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Exemplos de webhook do Microsoft Graph

#### <a name="sample-creating-a-subscription"></a>Exemplo: como criar uma assinatura

Suponha que você tenha o seguinte function.json que define um gatilho HTTP com uma associação de saída de assinatura usando a ação de criar:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir registra um webhook que notificará esse aplicativo de função quando o usuário que está chamando receber uma mensagem do Outlook:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

O exemplo de JS a seguir registra um webhook que notificará esse aplicativo de função quando o usuário que está chamando receber uma mensagem do Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Exemplo: manipulando notificações

Suponha que você tenha o seguinte function.json que defina um gatilho de webhook do Graph para lidar com mensagem do Outlook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir reage a mensagens de email de entrada e registra o corpo daquelas enviadas pelo destinatário e que contêm "Azure Functions" no assunto:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

O exemplo de JS a seguir reage a mensagens de email de entrada e registra o corpo daquelas enviadas pelo destinatário e que contêm "Azure Functions" no assunto:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>Exemplo: excluindo assinaturas

Suponha que você tenha o seguinte function.json que define um gatilho HTTP com uma associação de entrada de assinatura e uma associação de saída de assinatura usando a ação de excluir:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir obtém todas as assinaturas para o usuário que está chamando e, em seguida, as exclui:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

O exemplo de JS a seguir obtém todas as assinaturas para o usuário que está chamando e, em seguida, as exclui:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>Exemplo: atualizando assinaturas

Há duas abordagens para atualizar assinaturas:
- Use a identidade do aplicativo para lidar com todas as assinaturas. Isso requer o consentimento do administrador do Azure Active Directory. Isso pode ser usado por todos os idiomas com suporte no Azure Functions.
- Use a identidade associada a cada assinatura associando manualmente cada ID de usuário. Isso exigirá algum código personalizado para executar a associação. Isso pode ser usado somente por funções .NET.

Ambas as opções são mostradas abaixo.

**Como usar a identidade do aplicativo**

Suponha que você tenha o seguinte function.json que define um gatilho de temporizador com uma associação de entrada de assinatura e uma associação de saída de assinatura usando a identidade do aplicativo:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir atualiza as assinaturas em um temporizador usando a identidade do aplicativo:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

O exemplo de JS a seguir atualiza as assinaturas em um temporizador usando a identidade do aplicativo:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**Como usar identidades dinâmicas de usuário**

Para a opção alternativa, suponha que você tenha a seguinte function.json que defina um gatilho de temporizador, adiando a associação para a associação de entrada de assinatura ao código de função:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O exemplo de C# a seguir atualiza as assinaturas em um temporizador, usando a identidade de cada usuário ao criar a associação de saída no código:
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[gatilho HTTP]: functions-bindings-http-webhook.md
[Como trabalhar com webhooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
