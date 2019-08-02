---
title: Associações do Microsoft Graph do Azure Functions
description: Entenda como usar gatilhos e associações do Microsoft Graph no Azure Functions.
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: ae376343103d68df226d30d1d76133a3f055f5c2
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480381"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Associações do Microsoft Graph do Azure Functions

Este artigo explica como configurar e trabalhar com gatilhos e associações do Microsoft Graph no Azure Functions. Com isso, você pode usar o Azure Functions para trabalhar com eventos, percepções e dados do [Microsoft Graph](https://developer.microsoft.com/graph).

A extensão do Microsoft Graph oferece as seguintes associações:
- Um [associação de entrada do token de autenticação](#token-input) permite que você interaja com qualquer API do Microsoft Graph.
- Uma [associação de entrada de tabela do Excel](#excel-input) permite que você leia os dados do Excel.
- Uma [associação de saída de tabela do Excel](#excel-output) permite que você modifique os dados do Excel.
- Uma [associação de entrada de arquivo do OneDrive](#onedrive-input) permite que você leia arquivos no OneDrive.
- Uma [associação de saída de arquivo do OneDrive](#onedrive-output) permite que você grave arquivos no OneDrive.
- Uma [associação de saída de mensagem do Outlook](#outlook-output) permite que você envie email por meio do Outlook.
- Uma coleção de [gatilhos e associações do Microsoft Graph webhook](#webhooks) permite reagir a eventos do Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> As associações do Microsoft Graph estão em versão prévia no momento para Azure Functions versão 2.x. Eles não são suportados no Functions versão 1.x.

## <a name="packages"></a>Pacotes

A associação de entrada de token de autenticação é fornecida no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/). As outras associações do Microsoft Graph são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/). O código-fonte dos pacotes está no repositório GitHub [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Como configurar as extensões

As associações do Microsoft Graph estão disponíveis por meio de _extensões de associação_. Extensões de associação são componentes opcionais para o tempo de execução do Azure Functions. Esta seção mostra como configurar o Microsoft Graph e as extensões de token de autenticação.

### <a name="enabling-functions-20-preview"></a>Habilitar versão prévia do Functions 2.0

Extensões de associação só estão disponíveis para versão prévia do Azure Functions 2.0. 

Para obter informações sobre como configurar um aplicativo de funções para usar a versão prévia 2.0 do tempo de execução do Functions, confira [Como direcionar versões do tempo de execução do Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Como instalar a extensão

Para instalar uma extensão do portal do Azure, navegue até um modelo ou associação que faça referência a ela. Crie uma nova função e, enquanto estiver na tela de seleção de modelo, escolha o cenário "Microsoft Graph". Selecione um dos modelos desse cenário. Como alternativa, você pode navegar até a guia "Integração" de uma função existente e selecionar uma das associações abordadas neste artigo.

Em ambos os casos, um aviso será exibido especificando a extensão a ser instalada. Clique em **Instalar** para obter a extensão. Cada extensão precisa ser instalada apenas uma vez por aplicativo de funções. 

> [!Note] 
> O processo de instalação no portal pode levar até 10 minutos em um plano de consumo.

Se você estiver usando o Visual Studio, poderá obter as extensões instalando [os pacotes do NuGet que estão listados neste artigo](#packages).

### <a name="configuring-authentication--authorization"></a>Configurando a Autenticação/Autorização

As associações descritas neste artigo exigem que uma identidade seja usada. Isso permite que o Microsoft Graph aplique permissões e interações de auditoria. A identidade pode ser um usuário acessando seu aplicativo ou o aplicativo em si. Para configurar essa identidade, configure [Autenticação/Autorização do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) com o Azure Active Directory. Você também precisará solicitar quaisquer permissões de recurso que suas funções exijam.

> [!Note] 
> A extensão do Microsoft Graph tem suporte apenas para autenticação do Azure AD. Os usuários precisam fazer logon com uma conta corporativa ou de estudante.

Se estiver usando o portal do Azure, você verá um aviso abaixo do prompt para instalar a extensão. O aviso que solicita que você configure a Autenticação/Autorização do Serviço de Aplicativo e solicita quaisquer permissões de que o modelo ou associação precise. Clique em **Configurar Azure AD agora** ou **Adicionar permissões agora** conforme apropriado.



<a name="token-input"></a>
## <a name="auth-token"></a>Token de autenticação

Essas associações de entrada do token de autenticação obtêm um token do Azure AD para um determinado recurso e fornece-o ao seu código como uma cadeia de caracteres. O recurso pode ser qualquer um para o qual o aplicativo tenha permissões. 

Essa seção contém os seguintes procedimentos:

* [Exemplo](#auth-token---example)
* [Atributos](#auth-token---attributes)
* [Configuração](#auth-token---configuration)
* [Uso](#auth-token---usage)

### <a name="auth-token---example"></a>Token de autenticação - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token de autenticação - exemplo de script C#

O seguinte exemplo obtém informações do perfil do usuário.

O arquivo *function.json* define um gatilho HTTP com uma associação de entrada do token:

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

O código do script C# usa o token para fazer uma chamada HTTP para o Microsoft Graph e retorna o resultado:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Token de autenticação - exemplo de JavaScript

O seguinte exemplo obtém informações do perfil do usuário.

O arquivo *function.json* define um gatilho HTTP com uma associação de entrada do token:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código do JavaScript usa o token para fazer uma chamada HTTP para o Microsoft Graph e retorna o resultado.

```js
const rp = require('request-promise');

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

### <a name="auth-token---attributes"></a>Token de autenticação - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs).

### <a name="auth-token---configuration"></a>Token de autenticação - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Token` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatório – o nome da variável usada no código de função para o token de autenticação. Consulte [Como usar uma associação de entrada do token de autenticação do código](#token-input-code).|
|**type**||Obrigatório – deve ser definido como `token`.|
|**direction**||Obrigatório – deve ser definido como `in`.|
|**identidade**|**Identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**Recurso**|**resource**|Obrigatório – uma URL de recurso do Azure AD para a qual o token está sendo solicitado.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token de autenticação - uso

A associação em si não requer permissões do Azure AD, mas, dependendo de como o token é usado, talvez seja necessário solicitar permissões adicionais. Verifique os requisitos do recurso que você pretende acessar com o token.

O token sempre é apresentado ao código como uma cadeia de caracteres.

> [!Note]
> Ao desenvolver localmente com qualquer opção de `userFromId`, `userFromToken` ou `userFromRequest`, o token necessário pode ser [obtido manualmente](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) e especificado no cabeçalho de solicitação `X-MS-TOKEN-AAD-ID-TOKEN` de um aplicativo de cliente de chamada.


<a name="excel-input"></a>
## <a name="excel-input"></a>Entrada do Excel

Essa associação de entrada de tabela do Excel lê o conteúdo de uma tabela do Excel armazenada no OneDrive.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#excel-input---example)
* [Atributos](#excel-input---attributes)
* [Configuração](#excel-input---configuration)
* [Uso](#excel-input---usage)

### <a name="excel-input---example"></a>Entrada do Excel - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Entrada do Excel - exemplo de script C#

O arquivo *function.json* a seguir define um gatilho HTTP com uma associação de entrada do Excel:

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

O código do script C# a seguir lê o conteúdo da tabela especificada e retorna-o ao usuário:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Entrada do Excel - exemplo de JavaScript

O arquivo *function.json* a seguir define um gatilho HTTP com uma associação de entrada do Excel:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código do JavaScript a seguir lê o conteúdo da tabela especificada e retorna-o ao usuário.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Entrada do Excel - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-input---configuration"></a>Entrada do Excel - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Excel` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatório – o nome da variável usada no código de função para a tabela do Excel. Consulte [Como usar uma associação de entrada de tabela do Excel do código](#excel-input-code).|
|**type**||Obrigatório – deve ser definido como `excel`.|
|**direction**||Obrigatório – deve ser definido como `in`.|
|**identidade**|**Identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|**Caminho**|Obrigatório – o caminho no OneDrive para a pasta de trabalho do Excel.|
|**worksheetName**|**WorksheetName**|A planilha em que a tabela é encontrada.|
|**tableName**|**TableName**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Entrada do Excel - uso

Esta associação exige as seguintes permissões do Azure AD:

|Resource|Permissão|
|--------|--------|
|Microsoft Graph|Ler arquivos de usuário|

A associação expõe os seguintes tipos a funções .NET:
- string[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (usando o model binding estrutural)










<a name="excel-output"></a>
## <a name="excel-output"></a>Saída do Excel

A associação de saída do Excel modifica o conteúdo de uma tabela do Excel armazenada no OneDrive.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#excel-output---example)
* [Atributos](#excel-output---attributes)
* [Configuração](#excel-output---configuration)
* [Uso](#excel-output---usage)

### <a name="excel-output---example"></a>Saída do Excel - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Saída do Excel - exemplo de script C#

O exemplo a seguir adiciona linhas a uma tabela do Excel.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída do Excel:

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

O código do script C# adiciona uma nova linha à tabela (presumidamente de uma única coluna) com base na entrada da cadeia de caracteres de consulta:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
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

#### <a name="excel-output---javascript-example"></a>Saída do Excel - exemplo de JavaScript

O exemplo a seguir adiciona linhas a uma tabela do Excel.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída do Excel:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código do JavaScript a seguir adiciona uma nova linha à tabela (presumidamente de uma única coluna) com base na entrada da cadeia de caracteres de consulta.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Saída do Excel - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-output---configuration"></a>Saída do Excel - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Excel` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatório – o nome da variável usada no código de função para o token de autenticação. Consulte [Como usar uma associação de saída de tabela do Excel do código](#excel-output-code).|
|**type**||Obrigatório – deve ser definido como `excel`.|
|**direction**||Obrigatório – deve ser definido como `out`.|
|**identidade**|**Identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**UserId** |**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|**Caminho**|Obrigatório – o caminho no OneDrive para a pasta de trabalho do Excel.|
|**worksheetName**|**WorksheetName**|A planilha em que a tabela é encontrada.|
|**tableName**|**TableName**|O nome da tabela. Se não for especificado, o conteúdo da planilha será usado.|
|**updateType**|**UpdateType**|Obrigatório – o tipo de alteração a fazer na tabela. Pode ser um dos seguintes valores:<ul><li><code>update</code> – substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code> – adiciona a carga no final da tabela no OneDrive criando novas linhas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Saída do Excel - uso

Esta associação exige as seguintes permissões do Azure AD:

|Resource|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso completo aos arquivos do usuário|

A associação expõe os seguintes tipos a funções .NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (usando o model binding estrutural)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Entrada do arquivo

Essa associação de entrada do Arquivo do OneDrive lê o conteúdo de um arquivo armazenado no OneDrive.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#file-input---example)
* [Atributos](#file-input---attributes)
* [Configuração](#file-input---configuration)
* [Uso](#file-input---usage)

### <a name="file-input---example"></a>Entrada do Arquivo - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Entrada do Arquivo - exemplo de script C#

O exemplo a seguir lê um arquivo que está armazenado no OneDrive.

O arquivo *function.json* define um gatilho HTTP com uma associação de entrada do arquivo do OneDrive:

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

O código do script C# lê o arquivo especificado na cadeia de caracteres de consulta e registra seu comprimento:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Entrada do Arquivo - exemplo de JavaScript

O exemplo a seguir lê um arquivo que está armazenado no OneDrive.

O arquivo *function.json* define um gatilho HTTP com uma associação de entrada do arquivo do OneDrive:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código do JavaScript a seguir lê o arquivo especificado na cadeia de caracteres de consulta e registra seu comprimento.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Entrada do arquivo - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-input---configuration"></a>Entrada do arquivo - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `OneDrive` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatório – o nome da variável usada no código de função para o arquivo. Consulte [Como usar uma associação de entrada de arquivo do OneDrive do código](#onedrive-input-code).|
|**type**||Obrigatório – deve ser definido como `onedrive`.|
|**direction**||Obrigatório – deve ser definido como `in`.|
|**identidade**|**Identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|**Caminho**|Obrigatória – o caminho no OneDrive para o arquivo.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Entrada do arquivo - uso

Esta associação exige as seguintes permissões do Azure AD:

|Resource|Permissão|
|--------|--------|
|Microsoft Graph|Ler arquivos de usuário|

A associação expõe os seguintes tipos a funções .NET:
- byte[]
- Fluxo
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Saída do arquivo

Essa associação de saída do arquivo do OneDrive modifica o conteúdo de um arquivo armazenado no OneDrive.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#file-output---example)
* [Atributos](#file-output---attributes)
* [Configuração](#file-output---configuration)
* [Uso](#file-output---usage)

### <a name="file-output---example"></a>Saída do arquivo - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Saída do arquivo - exemplo de script C#

O exemplo a seguir grava um arquivo que está armazenado no OneDrive.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída do OneDrive:

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

O código do script C# obtém o texto da cadeia de caracteres de consulta e grava-o em um arquivo de texto (FunctionsTest.txt conforme definido na configuração acima) na raiz do OneDrive do chamador:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Saída do arquivo - exemplo de JavaScript

O exemplo a seguir grava um arquivo que está armazenado no OneDrive.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída do OneDrive:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O código do JavaScript obtém o texto da cadeia de caracteres de consulta e grava-o em um arquivo de texto (FunctionsTest.txt conforme definido na configuração acima) na raiz do OneDrive do chamador.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Saída do arquivo - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-output---configuration"></a>Saída do arquivo - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `OneDrive` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatório – o nome da variável usada no código de função para o arquivo. Consulte [Como usar uma associação de saída de arquivo do OneDrive do código](#onedrive-output-code).|
|**type**||Obrigatório – deve ser definido como `onedrive`.|
|**direction**||Obrigatório – deve ser definido como `out`.|
|**identidade**|**Identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**UserId** |**userId** |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**path**|**Caminho**|Obrigatória – o caminho no OneDrive para o arquivo.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Saída do arquivo - uso

Esta associação exige as seguintes permissões do Azure AD:

|Resource|Permissão|
|--------|--------|
|Microsoft Graph|Ter acesso completo aos arquivos do usuário|

A associação expõe os seguintes tipos a funções .NET:
- byte[]
- Fluxo
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Saída de Outlook

A associação de saída de mensagem do Outlook envia uma mensagem de email por meio do Outlook.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#outlook-output---example)
* [Atributos](#outlook-output---attributes)
* [Configuração](#outlook-output---configuration)
* [Uso](#outlook-output---usage)

### <a name="outlook-output---example"></a>Saída do Outlook - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Saída do Outlook - exemplo de script C#

O exemplo a seguir envia um email pelo Outlook.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída de mensagem do Outlook:

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

O código do script C# envia um email do chamador para um destinatário especificado na cadeia de caracteres de consulta:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
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

#### <a name="outlook-output---javascript-example"></a>Saída do Outlook - exemplo de JavaScript

O exemplo a seguir envia um email pelo Outlook.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída de mensagem do Outlook:

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

O código do JavaScript envia um email do chamador para um destinatário especificado na cadeia de caracteres de consulta:

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

### <a name="outlook-output---attributes"></a>Saída do Outlook - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs).

### <a name="outlook-output---configuration"></a>Saída do Outlook - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Outlook` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**type**||Obrigatório – deve ser definido como `outlook`.|
|**direction**||Obrigatório – deve ser definido como `out`.|
|**identidade**|**Identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Saída de Outlook - uso

Esta associação exige as seguintes permissões do Azure AD:

|Resource|Permissão|
|--------|--------|
|Microsoft Graph|Enviar email como usuário|

A associação expõe os seguintes tipos a funções .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Tipos de objetos personalizados (usando o model binding estrutural)






## <a name="webhooks"></a>webhooks

Webhooks permitem reagir a eventos no Microsoft Graph. Para dar suporte a webhooks, são necessárias funções para criar, atualizar e reagir a _assinaturas de webhook_. Uma solução completa de webhook exige uma combinação das seguintes associações:
- Um [gatilho de webhook do Microsoft Graph](#webhook-trigger) permite reagir a um webhook de entrada.
- Uma [associação de entrada de assinatura do webhook do Microsoft Graph](#webhook-input) permite que você liste as assinaturas existentes e, opcionalmente, atualize-as.
- Uma [associação de saída de assinatura de webhook do Microsoft Graph](#webhook-output) permite que você crie ou exclua assinaturas do webhook.

As associações em si não exigem permissões do Azure AD, mas você precisa solicitar permissões relevantes para o tipo de recurso ao qual você deseja reagir. Para obter uma lista de quais permissões são necessárias para cada tipo de recurso, consulte [permissões de assinatura](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Para obter mais informações sobre webhooks, confira [Como trabalhar com webhooks no Microsoft Graph].





## <a name="webhook-trigger"></a>Gatilho de webhook

O gatilho de webhook do Microsoft Graph permite que uma função reaja a um webhook de entrada do Microsoft Graph. Cada instância deste gatilho pode reagir a um tipo de recurso do Microsoft Graph.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#webhook-trigger---example)
* [Atributos](#webhook-trigger---attributes)
* [Configuração](#webhook-trigger---configuration)
* [Uso](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Gatilho de webhook - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Gatilho de webhook - exemplo de script C#

O exemplo a seguir manipula webhooks de mensagens de entrada do Outlook. Para usar um gatilho de webhook, [crie uma assinatura](#webhook-output---example) e poderá [atualizar a assinatura](#webhook-subscription-refresh) para impedir a expiração.

O arquivo *function.json* define um gatilho de webhook:

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

O código do script C# reage a mensagens de email de entrada e registra o corpo daquelas enviadas pelo destinatário e que contêm "Azure Functions" no assunto:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Gatilho de webhook - exemplo de JavaScript

O exemplo a seguir manipula webhooks de mensagens de entrada do Outlook. Para usar um gatilho de webhook, [crie uma assinatura](#webhook-output---example) e poderá [atualizar a assinatura](#webhook-subscription-refresh) para impedir a expiração.

O arquivo *function.json* define um gatilho de webhook:

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

O código do JavaScript reage a mensagens de email de entrada e registra o corpo daquelas enviadas pelo destinatário e que contêm "Azure Functions" no assunto:

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

### <a name="webhook-trigger---attributes"></a>Gatilho de webhook - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs).

### <a name="webhook-trigger---configuration"></a>Gatilho de webhook - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `GraphWebHookTrigger` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**type**||Obrigatório – deve ser definido como `graphWebhook`.|
|**direction**||Obrigatório – deve ser definido como `trigger`.|
|**resourceType**|**ResourceType**|Obrigatório – o recurso de grafo para o qual essa função deve responder a webhooks. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code> – alterações feitas em mensagens do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> – alterações feitas aos itens de raiz do OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> - as alterações feitas nos contatos pessoais do Outlook.</li><li><code>#Microsoft.Graph.Event</code> - as alterações feitas em itens de calendário do Outlook.</li></ul>|

> [!Note]
> Um aplicativo de funções só pode ter uma função que esteja registrada em relação a um determinado valor de `resourceType`.

### <a name="webhook-trigger---usage"></a>Gatilho de webhook - uso

A associação expõe os seguintes tipos a funções .NET:
- Tipos de SDK do Microsoft Graph relevantes ao tipo de recurso, por exemplo, `Microsoft.Graph.Message` ou `Microsoft.Graph.DriveItem`.
- Tipos de objetos personalizados (usando o model binding estrutural)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Entrada do webhook

A associação da entrada do webhook do Microsoft Graph permite recuperar a lista de assinaturas gerenciadas por este aplicativo de funções. A associação lê do armazenamento do aplicativo de funções e, assim, não reflete outras assinaturas criadas de fora do aplicativo.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#webhook-input---example)
* [Atributos](#webhook-input---attributes)
* [Configuração](#webhook-input---configuration)
* [Uso](#webhook-input---usage)

### <a name="webhook-input---example"></a>Entrada de webhook - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Entrada de webhook - exemplo de script C#

O exemplo a seguir obtém todas as assinaturas para o usuário que está chamando e as exclui.

O arquivo *function.json* define um gatilho HTTP com uma associação de entrada de assinatura e uma associação de saída de assinatura que usa a ação de excluir:

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

O código do script C# obtém as assinaturas e as exclui:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Entrada de webhook - exemplo de JavaScript

O exemplo a seguir obtém todas as assinaturas para o usuário que está chamando e as exclui.

O arquivo *function.json* define um gatilho HTTP com uma associação de entrada de assinatura e uma associação de saída de assinatura que usa a ação de excluir:

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

O código do JavaScript obtém as assinaturas e as exclui:

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

### <a name="webhook-input---attributes"></a>Entrada de webhook - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-input---configuration"></a>Entrada de webhook - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `GraphWebHookSubscription` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**type**||Obrigatório – deve ser definido como `graphWebhookSubscription`.|
|**direction**||Obrigatório – deve ser definido como `in`.|
|**filter**|**Filter**| Se definido como `userFromRequest`, a associação apenas recuperará assinaturas pertencentes ao usuário que está chamando (válido somente com [gatilho HTTP]).| 

### <a name="webhook-input---usage"></a>Entrada de webhook - uso

A associação expõe os seguintes tipos a funções .NET:
- string[]
- Matrizes de tipo de objeto personalizado
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Saída de webhook

Essa associação de saída de assinatura de webhook permite que você crie, exclua e atualize as assinaturas de webhook no Microsoft Graph.

Essa seção contém os seguintes procedimentos:

* [Exemplo](#webhook-output---example)
* [Atributos](#webhook-output---attributes)
* [Configuração](#webhook-output---configuration)
* [Uso](#webhook-output---usage)

### <a name="webhook-output---example"></a>Saída de webhook - exemplo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Saída de webhook - exemplo de script C#

O exemplo a seguir cria uma assinatura. Você pode [atualizar uma assinatura](#webhook-subscription-refresh) para impedir que ela expire.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída de assinatura usando a ação de criar:

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
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
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

O código do script C# registra um webhook que notificará esse aplicativo de funções quando o usuário que está chamando receber uma mensagem do Outlook:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Saída de webhook - exemplo de JavaScript

O exemplo a seguir cria uma assinatura. Você pode [atualizar uma assinatura](#webhook-subscription-refresh) para impedir que ela expire.

O arquivo *function.json* define um gatilho HTTP com uma associação de saída de assinatura usando a ação de criar:

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
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
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

O código do JavaScript registra um webhook que notificará esse aplicativo de funções quando o usuário que está chamando receber uma mensagem do Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Saída de webhook - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o atributo [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-output---configuration"></a>Saída de webhook - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `GraphWebHookSubscription` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**name**||Obrigatória – o nome da variável usada no código de função para a mensagem de email. Consulte [Como usar uma associação de saída da mensagem do Outlook do código](#outlook-output-code).|
|**type**||Obrigatório – deve ser definido como `graphWebhookSubscription`.|
|**direction**||Obrigatório – deve ser definido como `out`.|
|**identidade**|**Identidade**|Obrigatório – a identidade que será usada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code> – válida apenas com [gatilho HTTP]. Usa a identidade do usuário responsável pela chamada.</li><li><code>userFromId</code> – usa a identidade de um usuário conectado anteriormente com a ID especificada. Consulte a propriedade <code>userId</code>.</li><li><code>userFromToken</code> – usa a identidade representada pelo token especificado. Consulte a propriedade <code>userToken</code>.</li><li><code>clientCredentials</code> – usa a identidade do aplicativo de funções.</li></ul>|
|**userId**|**UserId**  |Necessário se, e somente se, a _identidade_ estiver definida como `userFromId`. Uma ID de entidade de usuário associada a um usuário conectado anteriormente.|
|**userToken**|**UserToken**|Necessário se, e somente se, a _identidade_ estiver definida como `userFromToken`. Um token válido para o aplicativo de função. |
|**action**|**Ação**|Obrigatório – especifica a ação que a associação deve executar. Pode ser um dos seguintes valores:<ul><li><code>create</code> – registrar uma nova assinatura.</li><li><code>delete</code> – exclui uma assinatura especificada.</li><li><code>refresh</code> – atualiza uma assinatura especificada para impedir que ela expire.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Necessário se e somente se a _ação_ estiver definida como `create`. Especifica o recurso Microsoft Graph que será monitorado quanto a alterações. Consulte [Como trabalhar com webhooks no Microsoft Graph]. |
|**changeType**|**ChangeType**|Necessário se e somente se a _ação_ estiver definida como `create`. Indica o tipo de alteração no recurso assinado que gerará uma notificação. Os valores com suporte são: `created`, `updated`, `deleted`. Você pode combinar diversos valores usando uma lista separada por vírgulas.|

### <a name="webhook-output---usage"></a>Saída de webhook - uso

A associação expõe os seguintes tipos a funções .NET:
- string
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Atualização de assinatura de webhook

Há duas abordagens para atualizar assinaturas:

- Use a identidade do aplicativo para lidar com todas as assinaturas. Isso requer o consentimento do administrador do Azure Active Directory. Isso pode ser usado por todos os idiomas com suporte no Azure Functions.
- Use a identidade associada a cada assinatura associando manualmente cada ID de usuário. Isso exigirá algum código personalizado para executar a associação. Isso pode ser usado somente por funções .NET.

Esta seção contém um exemplo para cada uma destas abordagens:

* [Exemplo de identidade de aplicativo](#webhook-subscription-refresh---app-identity-example)
* [Exemplo de identidade de usuário](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Atualização de assinatura de Webhook - exemplo de identidade de aplicativo

Consulte o exemplo específico a um idioma:

* [Script do C# (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade de aplicativo - exemplo de script C#

O exemplo a seguir usa a identidade do aplicativo para atualizar uma assinatura.

O arquivo *function.json* define um gatilho de temporizador com uma associação de entrada de assinatura e uma associação de saída de assinatura:

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

O código do script C# atualiza as assinaturas:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Atualização de identidade de aplicativo - exemplo de script C#

O exemplo a seguir usa a identidade do aplicativo para atualizar uma assinatura.

O arquivo *function.json* define um gatilho de temporizador com uma associação de entrada de assinatura e uma associação de saída de assinatura:

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

O código do JavaScript atualiza as assinaturas:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Atualização de assinatura de Webhook - exemplo de identidade de usuário

O exemplo a seguir utiliza a identidade do usuário para atualizar uma assinatura.

O arquivo *function.json* define um gatilho de temporizador e adia a associação de entrada de assinatura para o código de função:

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

O código do script C# atualiza as assinaturas e cria a associação de saída no código usando a identidade de cada usuário:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
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
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

[Gatilho HTTP]: functions-bindings-http-webhook.md
[Como trabalhar com webhooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
