---
title: Referência do desenvolvedor de JavaScript do Azure Functions | Microsoft Docs
description: Entenda como desenvolver funções usando JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 9a7c186f7c5fb46078eaa5729e79fdcc256ecc6d
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460208"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia do desenvolvedor de JavaScript do Azure Functions

Este guia contém informações sobre as complexidades de escrever Azure Functions com JavaScript.

Uma função JavaScript é um `function` exportado que é executado quando acionado ([acionadores são configurados em function.json](functions-triggers-bindings.md)). O primeiro argumento passado para cada função é um `context` objeto, que é usado para envio e recebimento de dados, registro em log e se comunicar com o tempo de execução.

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Conclua o início rápido de funções para criar sua primeira função, usando [Visual Studio Code](functions-create-first-function-vs-code.md) ou [no portal do](functions-create-first-azure-function.md).

Este artigo também suporta [desenvolvimento de aplicativos do TypeScript](#typescript).

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas necessárias para um projeto JavaScript é semelhante à seguinte. Este padrão pode ser alterado. Para mais informações, consulte a seção [scriptArquivo](#using-scriptfile) abaixo.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

Na raiz do projeto, há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (.js) e o arquivo de configuração de associação (function.json). O nome do diretório pai de `function.json` é sempre o nome da sua função.

As extensões de associação necessárias na [versão 2.x](functions-versions.md) do tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você precisa [registrar as extensões de associação](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="exporting-a-function"></a>Exportando uma função

As funções JavaScript precisam ser exportadas por meio de [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Sua função exportada deve ser uma função JavaScript que é executada quando acionada.

Por padrão, o tempo de execução de Funções procura sua função em `index.js`, onde `index.js` compartilha o mesmo diretório pai que o `function.json` correspondente. No caso padrão, sua função exportada deve ser a única exportação de seu arquivo ou a exportação denominada `run` ou `index`. Para configurar o local do arquivo e o nome de exportação da função, leia [Configurando o ponto de entrada da função](functions-reference-node.md#configure-function-entry-point) abaixo.

Sua função exportada é passada um número de argumentos na execução. O primeiro argumento é sempre um objeto `context`. Se sua função é síncrona (não retorna uma promessa), você deve passar o `context` do objeto, como chamar `context.done` é necessária para o uso correto.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportando uma função assíncrona
Ao usar a declaração [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) ou o JavaScript simples [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) na versão 2.x do tempo de execução Functions, não é necessário chamar explicitamente o [`context.done`](#contextdone-method) retorno de chamada para sinalizar que sua função foi concluída. Sua função é concluída quando a função assíncrona exportada / Promise é concluída. Para funções que visam o tempo de execução da versão 1.x, você ainda deve chamar [`context.done`](#contextdone-method) quando seu código terminar de ser executado.

O exemplo a seguir é uma função simples que registra que foi acionada e imediatamente conclui a execução.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Ao exportar uma função assíncrona, você também pode configurar uma ligação de saída para obter o valor `return`. Isso é recomendado se você tiver apenas uma associação de saída.

Para atribuir uma saída usando `return`, altere o `name` propriedade a ser `$return` em `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Nesse caso, sua função deve se parecer com o seguinte exemplo:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Associações 
Em JavaScript, [ligações](functions-triggers-bindings.md) são configuradas e definidas na função function.json. As funções interagem com as ligações de várias maneiras.

### <a name="inputs"></a>Entradas
As entradas são divididas em duas categorias no Azure Functions: uma é a entrada de gatilho e a outra é a entrada adicional. Trigger e outras ligações de entrada (ligações de `direction === "in"`) podem ser lidas por uma função de três maneiras:
 - ** _[Recomendado]_ Como parâmetros passados para sua função.** Eles são passados para a função na mesma ordem em que são definidos *function.json*. O `name` propriedade definida no *Function. JSON* não precisa corresponder ao nome do parâmetro, embora deveria.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Como os membros de [`context.bindings`](#contextbindings-property) objeto.** Observe que a propriedade `name` definida em *function.json* não precisa corresponder ao nome do seu parâmetro, embora deva....
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como entradas usando o [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx)objeto JavaScript.** Isso é essencialmente o mesmo que passar entradas como parâmetros, mas permite que você manipule dinamicamente entradas.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
As saídas (ligações de `direction === "out"`) podem ser gravadas por uma função de várias maneiras. Em todos os casos, a propriedade `name` da ligação, conforme definido em *function.json*, corresponde ao nome do membro do objeto gravado na sua função. 

Você pode atribuir os dados para as associações de saída em uma das seguintes maneiras (não o combine esses métodos):

- ** _[Recomendado para várias saídas]_ Retornando um objeto.** Se você estiver usando uma async/promessa de retorno de função, você pode retornar um objeto com dados de saída atribuído. No exemplo abaixo, as ligações de saída são nomeadas "httpResponse" e "queueOutput" em *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Se você estiver usando uma função síncrona, você pode retornar este objeto usando [`context.done`](#contextdone-method) (veja o exemplo).
- ** _[Recomendado para saída única]_ Retornando um valor diretamente e usando o nome de ligação $ return.** Isso funciona apenas para as funções de retorno assíncrono / Promessa. Veja o exemplo em [exportando uma função assíncrona](#exporting-an-async-function). 
- **Atribuindo valores a serem `context.bindings`**  você pode atribuir valores diretamente para Context. Bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Tipo de dados de associações

Para definir o tipo de dados para uma associação de entrada, use a propriedade `dataType` na definição de associação. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, use o tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

As opções para `dataType` são: `binary`, `stream` e `string`.

## <a name="context-object"></a>objeto de contexto
O tempo de execução usa um objeto `context` para passar dados de/para sua função e permitir que você se comunique com o tempo de execução. O objeto de contexto pode ser usado para ler e definir os dados de associações, gravar logs e usando o `context.done` retorno de chamada quando a função exportada é síncrona.

O `context` objeto é sempre o primeiro parâmetro para uma função. Deve ser incluído porque tem métodos importantes, como `context.done` e `context.log`. Você pode nomear o objeto de acordo com a sua preferência (por exemplo, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Propriedade context.bindings

```js
context.bindings
```

Retorna um objeto nomeado que é usado para ler ou atribuir a associação de dados. Entrada e dados de associação de gatilho podem ser acessados por propriedades de leitura em `context.bindings`. Dados de associação de saída podem ser atribuídos ao adicionar dados a `context.bindings`

Por exemplo, as seguintes definições de ligação em sua função.json permitem acessar o conteúdo de uma fila de `context.bindings.myInput`e atribuir saídas a uma fila usando`context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Você pode optar por definir os dados de ligação de saída usando o método `context.done` em vez do objeto `context.binding` (veja abaixo).

### <a name="contextbindingdata-property"></a>Propriedade context.bindingData

```js
context.bindingData
```

Retorna um objeto nomeado que contém dados de invocação de função e os metadados do gatilho (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Para obter um exemplo de metadados de gatilho, confira este [exemplo de hubs de eventos](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Método context.done

```js
context.done([err],[propertyBag])
```

Permite que o tempo de execução saiba que seu código foi concluído. Quando sua função usa a declaração [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function), você não precisa usar`context.done()`. O retorno de chamada `context.done` é chamado implicitamente. As funções assíncronas estão disponíveis no Node.js 8 ou em uma versão posterior, que requer a versão 2.x do tempo de execução do Functions.

Se a função não é uma função assíncrona, **você precisa chamar** `context.done` para informar ao tempo de execução que a função está concluída. Os tempos de execução se está ausente.

O método `context.done` permite que você retorne um erro definido pelo usuário ao tempo de execução e um objeto JSON que contém dados de associação de saída. As propriedades transmitidas para `context.done`sobrescrevem qualquer coisa definida no objeto`context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Método context.log  

```js
context.log(message)
```

Permite que você grave em logs de função de streaming no nível de rastreamento padrão. No `context.log`, há métodos de registro adicionais disponíveis para permitir que você grave logs de função em outros níveis de rastreamento:


| Método                 | DESCRIÇÃO                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Grava no registro em log no nível do erro, ou em um nível inferior.   |
| **warn(_message_)**    | Grava no registro em log no nível do aviso, ou em um nível inferior. |
| **info(_message_)**    | Grava no registro em log no nível da informação, ou em um nível inferior.    |
| **verbose(_message_)** | Grava no registro em log no nível detalhado.           |

O exemplo a seguir grava um log no nível de rastreamento de aviso:

```javascript
context.log.warn("Something has happened."); 
```

Você pode [configurar o limite do nível de rastreamento para registro em log](#configure-the-trace-level-for-console-logging) no arquivo host.json. Para obter mais informações sobre como gravar logs, confira [Gravando saídas de rastreamento](#writing-trace-output-to-the-console) abaixo.

Leia [Monitorado o Azure Functions](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

## <a name="writing-trace-output-to-the-console"></a>Gravar a saída de rastreamento no console 

No Functions, use os métodos `context.log` para gravar a saída de rastreamento no console. No Functions v2.x, as saídas de rastreio usando `console.log` são capturadas no nível do Aplicativo Function. Isso significa que as saídas da `console.log` não estão vinculados a uma invocação de função específica e não são exibidos nos logs da função específicos. Eles, no entanto, se propagam para o Application Insights. No Functions v1.x, não é possível usar `console.log` para gravar no console.

Quando você chama `context.log()`, sua mensagem é gravada no console no nível de rastreamento padrão, que é o nível de rastreamento de _informações_. O código a seguir grava no console no nível de rastreamento de informações:

```javascript
context.log({hello: 'world'});  
```

Esse código é equivalente ao código acima:

```javascript
context.log.info({hello: 'world'});  
```

Esse código grava no console no nível de erro:

```javascript
context.log.error("An error has occurred.");  
```

Como _erro_ é o nível de rastreamento mais alto, esse rastreamento é gravado na saída em todos os níveis de rastreamento enquanto o registro em log estiver habilitado.

Todos os métodos `context.log` dão suporte ao mesmo formato de parâmetro que o [método util.format](https://nodejs.org/api/util.html#util_util_format_format) de Node.js. Considere o código a seguir, que grava logs de função usando o nível de rastreamento padrão:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Você também pode escrever o mesmo código no formato a seguir:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurar o nível de rastreamento para o registro em log no console

O Functions 1.x permite a definição do nível de rastreamento de limite para gravar no console, o que facilita o controle do modo de gravação dos rastreamentos no console da sua função. Para definir o limite para todos os rastreamentos gravados no console, use a propriedade `tracing.consoleLevel` no arquivo host.json. Essa configuração se aplica a todas as funções em seu aplicativo de função. O exemplo a seguir define o limite de rastreamento para habilitar o registro em log detalhado:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Os valores de **consoleLevel** correspondem aos nomes dos métodos `context.log`. Para desabilitar todo o registro em log do rastreamento no console, defina **consoleLevel** como _off_. Para obter mais informações, consulte a [referência para host.json](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

HTTP e gatilhos de webhook e associações de saída HTTP usam objetos de solicitação e resposta para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto da solicitação

O objeto `context.req` (solicitação) tem as seguintes propriedades:

| Propriedade      | DESCRIÇÃO                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Um objeto que contém o corpo da solicitação.               |
| _headers_     | Um objeto que contém os cabeçalhos da solicitação.                   |
| _method_      | O método HTTP da solicitação.                                |
| _originalUrl_ | A URL da solicitação.                                        |
| _params_      | Um objeto que contém os parâmetros de roteamento da solicitação. |
| _query_       | Um objeto que contém os parâmetros da consulta.                  |
| _rawBody_     | O corpo da mensagem como uma cadeia de caracteres.                           |


### <a name="response-object"></a>Objeto de resposta

O objeto `context.res` (resposta) tem as seguintes propriedades:

| Propriedade  | DESCRIÇÃO                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Um objeto que contém o corpo da resposta.         |
| _headers_ | Um objeto que contém os cabeçalhos da resposta.             |
| _isRaw_   | Indica que a formatação foi ignorada para a resposta.    |
| _status_  | O código de status HTTP da resposta.                     |

### <a name="accessing-the-request-and-response"></a>Acessar a solicitação e a resposta 

Ao trabalhar com gatilhos HTTP, há várias maneiras de acessar os objetos de solicitação e resposta HTTP:

+ **Partir `req` e `res` propriedades no `context` objeto.** Dessa forma, você pode usar o padrão convencional para acessar os dados HTTP a partir do objeto de contexto, em vez de usar o padrão `context.bindings.name` completo. O exemplo a seguir mostra como acessar os objetos `req` e `res` no `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Na entrada nomeada e associações de saída.** Dessa forma, o gatilho e as associações de HTTP funcionam da mesma forma que qualquer outra associação. O exemplo a seguir define o objeto de resposta usando uma associação chamada `response`: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ ** _[Somente Resposta]_ Chamando`context.res.send(body?: any)`.** Uma resposta HTTP é criada com a entrada `body` como o corpo da resposta. `context.done()` é chamado implicitamente.

+ ** _[Somente Resposta]_ Chamando`context.done()`.** Um tipo especial de associação HTTP retorna a resposta que é passada para o `context.done()` método. A seguinte associação de saída HTTP define um parâmetro de saída `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Versão do Node.js

A tabela a seguir mostra a versão do Node.js usada por cada versão principal do tempo de execução do Functions:

| Versão do Functions | Versão do Node.js | 
|---|---|
| 1.x | 6.11.2 (bloqueada pelo tempo de execução) |
| 2. x  | _Active Directory LTS_ e _LTS manutenção_ versões de Node. js (8.11.1 e 10.14.1 recomendado). Defina a versão usando a [configuração do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_DEFAULT_NODE_VERSION.|

Veja versão atual que o tempo de execução está usando verificando a configuração de aplicativo acima ou imprimindo `process.version` de qualquer função.

## <a name="dependency-management"></a>Gerenciamento de dependências
Para usar as bibliotecas da comunidade no código JavaScript, como é mostrado no exemplo abaixo, você precisa garantir que todas as dependências sejam instaladas no aplicativo de funções no Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Você deve definir um arquivo `package.json` na raiz do seu aplicativo de função. A definição de arquivo permite que todas as funções no aplicativo compartilhem os mesmos pacotes armazenados em cache, o que oferece o melhor desempenho. Se houver conflitos de versão, você poderá resolver o conflito adicionando um arquivo `package.json` na pasta de uma função específica.  

Ao implantar os aplicativos de função do controle de origem, qualquer arquivo `package.json` presente em seu repositório acionará um `npm install` em sua pasta durante a implantação. Mas ao implantar via Portal ou CLI, você terá que instalar manualmente os pacotes.

Há duas maneiras de instalar pacotes no aplicativo de funções: 

### <a name="deploying-with-dependencies"></a>Implantando com dependências
1. Instale todos os pacotes necessários localmente executando `npm install`.

2. Implante o código e verifique se a pasta `node_modules` está incluída na implantação. 


### <a name="using-kudu"></a>Usando o Kudu
1. Vá para `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Console de Depuração** > **CMD**.

3. Acesse `D:\home\site\wwwroot`e arraste o arquivo package.json para a pasta **wwwroot** na metade superior da página.  
    Também há outras maneiras de carregar arquivos em seu aplicativo de função. Para saber mais, confira [Como atualizar os arquivos do aplicativo de função](functions-reference.md#fileupdate). 

4. Depois que o arquivo package.json é carregado, execute o comando `npm install` no **console de execução remota do Kudu**.  
    Essa ação baixa os pacotes indicados no arquivo package.json e reinicia o aplicativo de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](functions-app-settings.md), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando `process.env`, conforme mostrado aqui nas chamadas de segundo e terceira `context.log()` onde podemos fazer a `AzureWebJobsStorage` e `WEBSITE_SITE_NAME` variáveis de ambiente:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as configurações do aplicativo são lidos a partir de [Settings](functions-run-local.md#local-settings-file) arquivo de projeto.

## <a name="configure-function-entry-point"></a>Configurar o ponto de entrada de função

As propriedades `scriptFile` e `entryPoint` do `function.json` podem ser usadas para configurar o local e o nome da função exportada. Essas propriedades podem ser importantes quando seu JavaScript é transferido.

### <a name="using-scriptfile"></a>Usando o `scriptFile`

Por padrão, uma função JavaScript é executada do `index.js`, um arquivo que compartilha o mesmo diretório pai que seu `function.json` correspondente.

`scriptFile` pode ser usado para obter uma estrutura de pastas semelhante ao exemplo a seguir:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

O `function.json` da `myNodeFunction` deve incluir uma propriedade `scriptFile` que aponte para o arquivo com a função exportada a ser executada.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Usando o `entryPoint`

No `scriptFile` (ou `index.js`), uma função precisa ser exportada usando `module.exports` para ser localizada e executada. Por padrão, a função que é executada quando disparada é a única exportação desse arquivo, a exportação denominada `run` ou a exportação denominada `index`.

Isso pode ser configurado usando `entryPoint` em `function.json`, como no exemplo a seguir:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

No Functions v2.x, que suporta o parâmetro `this` nas funções do usuário, o código da função poderia ser como no seguinte exemplo:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Neste exemplo, é importante observar que, embora um objeto está sendo exportado, não há nenhuma garantia para preservar o estado entre as execuções.

## <a name="local-debugging"></a>Depuração local

Quando iniciado com o `--inspect` parâmetro, um processo do Node. js escuta para um cliente de depuração na porta especificada. No Azure Functions 2.x, você pode especificar argumentos para passar para o processo do Node. js que executa o código adicionando a variável de ambiente ou configuração de aplicativo `languageWorkers:node:arguments = <args>`. 

Para depurar localmente, adicione `"languageWorkers:node:arguments": "--inspect=5858"` sob `Values` no seu [Settings](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) de arquivo e anexar um depurador para a porta 5858.

Durante a depuração usando o VS Code, o `--inspect` parâmetro é adicionado automaticamente usando o `port` valor no arquivo Launch JSON do projeto.

Na versão 1. x, definindo `languageWorkers:node:arguments` não funcionará. A porta de depuração pode ser selecionada com o [ `--nodeDebugPort` ](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parâmetro sobre as ferramentas básicas do Azure Functions.

## <a name="typescript"></a>TypeScript

Quando você seleciona a versão 2.x do tempo de execução de funções, ambos [Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md) e o [ferramentas do Azure Functions Core](functions-run-local.md) permitem que você crie aplicativos de funções usando um modelo que oferecem suporte a Projetos de aplicativo de função do TypeScript. O modelo gera `package.json` e `tsconfig.json` arquivos de projeto que o tornam mais fácil para transcompilar, executar e publicar funções de JavaScript de código do TypeScript com essas ferramentas.

Uma gerada `.funcignore` arquivo é usado para indicar quais arquivos são excluídos quando um projeto é publicado no Azure.  

Arquivos TypeScript (. TS) são transcompilado em arquivos JavaScript (. js) na `dist` diretório de saída. Modelos do TypeScript usam o [ `scriptFile` parâmetro](#using-scriptfile) na `function.json` para indicar o local do arquivo. js correspondente no `dist` pasta. O local de saída é definido pelo modelo, usando `outDir` parâmetro no `tsconfig.json` arquivo. Se você alterar essa configuração ou o nome da pasta, o tempo de execução não é possível encontrar o código seja executado.

> [!NOTE]
> Suporte experimental para TypeScript existe versão 1.x do tempo de execução de funções. A versão experimental transpiles arquivos TypeScript em arquivos JavaScript quando a função é invocada. Na versão 2. x, esse suporte experimental foi substituído pelo método controlado por ferramenta que faz a transcompilação antes que o host seja inicializado e durante o processo de implantação.

A maneira que você desenvolve localmente e implantar a partir de um projeto TypeScript depende de sua ferramenta de desenvolvimento.

### <a name="visual-studio-code"></a>Visual Studio Code

O [Azure Functions para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extensão permite que você desenvolva suas funções usando o TypeScript. As ferramentas essenciais é um requisito da extensão do Azure Functions.

Para criar um aplicativo de funções de TypeScript no Visual Studio Code, escolha `TypeScript` como sua linguagem quando você cria um aplicativo de funções.

Quando você pressiona **F5** para executar o aplicativo localmente, que a transcompilação é feita antes que o host (func.exe) seja inicializado. 

Quando você implanta seu aplicativo de funções para o Azure usando o **implantar no aplicativo de funções...**  botão, a extensão de funções do Azure primeiro gera uma compilação pronta para produção de arquivos JavaScript dos arquivos de origem TypeScript.

### <a name="azure-functions-core-tools"></a>Ferramentas básicas do Azure Functions

Para criar um projeto de aplicativo de função do TypeScript usando ferramentas básicas, você deve especificar a opção de linguagem TypeScript quando você cria seu aplicativo de funções. Você pode fazer isso em uma das seguintes maneiras:

- Execute o `func init` comando, selecione `node` como a pilha de idiomas e, em seguida, selecione `typescript`.

- Execute o comando `func init --worker-runtime typescript`.

Para executar o código do aplicativo de função localmente usando as ferramentas básicas, use o `npm start` comando, em vez de `func host start`. O `npm start` comando é equivalente aos comandos a seguir:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Antes de usar o [ `func azure functionapp publish` ] comando para implantar no Azure, primeiro você deve executar o `npm run build:production` comando. Este comando cria uma compilação pronta para produção de arquivos JavaScript dos arquivos de origem TypeScript que podem ser implantados usando [ `func azure functionapp publish` ].

## <a name="considerations-for-javascript-functions"></a>Considerações para funções em JavaScript

Ao trabalhar com funções JavaScript, lembre-se das considerações nas seções a seguir.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolher Planos do Serviço de Aplicativo de vCPU único

Ao criar um aplicativo de funções que usa o Plano do Serviço de Aplicativo, recomendamos que você selecione um plano de vCPU único em vez de um plano com vários vCPUs. Atualmente, o Functions executa funções em JavaScript com mais eficiência em VMs de vCPU único, e o uso de VMs maiores não produz os aprimoramentos de desempenho esperados. Quando necessário, você pode escalar horizontalmente manualmente adicionando mais instâncias VM de vCPU único, ou você pode habilitar o dimensionamento automático. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Inicialização a frio

No desenvolvimento de Azure Functions no modelo de hospedagem sem servidor, as inicializações a frio são uma realidade. *Partida a frio* refere-se ao fato de que, quando seu aplicativo de função é iniciado pela primeira vez após um período de inatividade, leva mais tempo para inicializar. Para funções JavaScript com árvores de dependência grandes em particular, o cold start pode ser significativo. Para acelerar o processo de inicialização a frio, [execute suas funções como um arquivo de pacote](run-functions-from-deployment-package.md) quando possível. Muitos métodos de implantação usam a execução do modelo de pacote por padrão, mas se você estiver experimentando grandes reinicializações a frio e não estiver sendo executado dessa maneira, essa alteração poderá oferecer uma melhoria significativa.

### <a name="connection-limits"></a>Limites de Conexão

Quando você usa um cliente específico do serviço em um aplicativo de funções do Azure, não crie um novo cliente com cada invocação de função. Em vez disso, crie um único cliente estático no escopo global. Para obter mais informações, consulte [Gerenciando conexões no Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Use `async` e `await`

Ao escrever o Azure Functions em JavaScript, você deve escrever código usando o `async` e `await` palavras-chave. Escrever código usando `async` e `await` em vez de retornos de chamada ou `.then` e `.catch` com promessas ajuda a evitar dois problemas comuns:
 - Lançando exceções não identificadas que [falhas de processo do Node. js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potencialmente afetar a execução de outras funções.
 - Comportamento inesperado, como logs ausentes de context. log, causadas por chamadas assíncronas que não são aguardadas corretamente.

No exemplo a seguir, o método assíncrono `fs.readFile` é invocado com uma função de retorno de chamada do erro, primeiro como seu segundo parâmetro. Esse código faz com que ambos os problemas mencionados acima. Uma exceção que não é capturada explicitamente no escopo correto travou todo o processo (problema #1). Chamar `context.done()` fora do escopo do retorno de chamada função significa que a invocação de função pode terminar antes que o arquivo é lido (problema #2). Neste exemplo, chamando `context.done()` a partir de entradas de log de resultados muito cedo no ausente `Data from file:`.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Usando o `async` e `await` palavras-chave ajuda a evitar ambos esses erros. Você deve usar a função de utilitário do Node. js [ `util.promisify` ](https://nodejs.org/api/util.html#util_util_promisify_original) para ativar funções de estilo de retorno de chamada do primeiro erro nas funções awaitable.

O exemplo a seguir, qualquer exceção sem tratamento gerada durante a execução de função falha a invocação individual que gerou uma exceção. O `await` palavra-chave significa que etapas abaixo `readFileAsync` executado somente após `readFile` for concluída. Com o `async` e `await`, você também não precisa chamar o `context.done()` retorno de chamada.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    try {
        const data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

+ [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
+ [Referência do desenvolvedor do Azure Functions](functions-reference.md)
+ [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)

['func azure functionapp publish']: functions-run-local.md#project-file-deployment
