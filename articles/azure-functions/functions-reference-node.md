---
title: Referência do desenvolvedor de JavaScript para Azure Functions
description: Entenda como desenvolver funções usando JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 11/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3e99b156d220b4c24a368886b1c0ca0813ffdc51
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674126"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia do desenvolvedor de JavaScript do Azure Functions

Este guia contém informações detalhadas para ajudá-lo a desenvolver com sucesso Azure Functions usando o JavaScript.

Como um Express.js, Node.js ou desenvolvedor de JavaScript, se você for novo no Azure Functions, considere primeiro ler um dos seguintes artigos:

| Introdução | Conceitos| Aprendizagem orientada |
| -- | -- | -- | 
| <ul><li>[Node.js função usando Visual Studio Code](./create-first-function-vs-code-node.md)</li><li>[ FunçãoNode.js com terminal/prompt de comando](./create-first-function-cli-node.md)</li></ul> | <ul><li>[Guia do desenvolvedor](functions-reference.md)</li><li>[Opções de hospedagem](functions-scale.md)</li><li>[Funções do TypeScript](#typescript)</li><li>[&nbsp;Considerações sobre desempenho](functions-best-practices.md)</li></ul> | <ul><li>[Criar aplicativos sem servidor](/learn/paths/create-serverless-applications/)</li><li>[Refatorar Node.js e APIs expressas para APIs sem servidor](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>Noções básicas da função JavaScript

Uma função JavaScript (Node.js) é uma exportação `function` que é executada quando disparada (os[gatilhos são configurados no function.jsem](functions-triggers-bindings.md)). O primeiro argumento passado para cada função é um `context` objeto, que é usado para receber e enviar dados de associação, registro em log e comunicação com o tempo de execução.

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

As extensões de associação necessárias na [versão 2.x](functions-versions.md) do runtime do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você precisa [registrar as extensões de associação](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="exporting-a-function"></a>Exportando uma função

As funções JavaScript devem ser exportadas via [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports) ). Sua função exportada deve ser uma função JavaScript que é executada quando acionada.

Por padrão, o runtime de Funções procura sua função em `index.js`, onde `index.js` compartilha o mesmo diretório pai que o `function.json` correspondente. No caso padrão, sua função exportada deve ser a única exportação de seu arquivo ou a exportação denominada `run` ou `index`. Para configurar o local do arquivo e o nome de exportação da função, leia [Configurando o ponto de entrada da função](functions-reference-node.md#configure-function-entry-point) abaixo.

Sua função exportada é passada um número de argumentos na execução. O primeiro argumento é sempre um objeto `context`. Se sua função for síncrona (não retorna uma promessa), você deve passar o `context` objeto, pois a chamada `context.done` é necessária para uso correto.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportando uma função assíncrona
Ao usar a [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaração ou as [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) de JavaScript simples na versão 2. x do tempo de execução do functions, você não precisa chamar explicitamente o [`context.done`](#contextdone-method) retorno de chamada para sinalizar que a função foi concluída. Sua função é concluída quando a função assíncrona exportada / Promise é concluída. Para funções direcionadas ao tempo de execução da versão 1. x, você ainda deve chamar [`context.done`](#contextdone-method) quando o código é executado.

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
 - **_[Recomendado]_ Como parâmetros passados para sua função.** Eles são passados para a função na mesma ordem em que são definidos *function.json*. A `name` propriedade definida no *function.jsem* não precisa corresponder ao nome do parâmetro, embora deva ser.
 
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
   
 - **Como entradas usando o [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)objeto JavaScript.** Isso é essencialmente o mesmo que passar entradas como parâmetros, mas permite que você manipule dinamicamente entradas.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Saídas
As saídas (ligações de `direction === "out"`) podem ser gravadas por uma função de várias maneiras. Em todos os casos, a propriedade `name` da ligação, conforme definido em *function.json*, corresponde ao nome do membro do objeto gravado na sua função. 

Você pode atribuir dados a associações de saída de uma das seguintes maneiras (não Combine esses métodos):

- **_[Recomendado para várias saídas]_ Retornando um objeto.** Se você estiver usando uma função de retorno de Async/Promise, poderá retornar um objeto com os dados de saída atribuídos. No exemplo abaixo, as ligações de saída são nomeadas "httpResponse" e "queueOutput" em *function.json*.

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

  Se você estiver usando uma função síncrona, poderá retornar esse objeto usando [`context.done`](#contextdone-method) (consulte o exemplo).
- **_[Recomendado para saída única]_ Retornando um valor diretamente e usando o nome de ligação $ return.** Isso funciona apenas para as funções de retorno assíncrono / Promessa. Veja o exemplo em [exportando uma função assíncrona](#exporting-an-async-function). 
- **Atribuindo valores a serem `context.bindings`** você pode atribuir valores diretamente para Context. Bindings.

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

O tempo de execução usa um `context` objeto para passar dados de e para sua função e o tempo de execução. Usado para ler e definir dados de associações e para gravar em logs, o `context` objeto é sempre o primeiro parâmetro passado para uma função.

Para funções que apresentam código síncrono, o objeto de contexto inclui o retorno de chamada `done` que você chama quando a função é feita no processamento. Chamar explicitamente `done` é desnecessário ao escrever código assíncrono; o retorno de chamada `done` é chamado implicitamente.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

O contexto passado para sua função expõe uma `executionContext` propriedade, que é um objeto com as seguintes propriedades:

| Nome da propriedade  | Type  | Descrição |
|---------|---------|---------|
| `invocationId` | String | Fornece um identificador exclusivo para a invocação de função específica. |
| `functionName` | String | Fornece o nome da função em execução |
| `functionDirectory` | String | Fornece o diretório de aplicativos do functions. |

O exemplo a seguir mostra como retornar o `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>Propriedade context.bindings

```js
context.bindings
```

Retorna um objeto nomeado que é usado para ler ou atribuir dados de associação. Dados de associação de entrada e gatilho podem ser acessados lendo propriedades em `context.bindings` . Os dados de associação de saída podem ser atribuídos adicionando dados a `context.bindings`

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

Retorna um objeto nomeado que contém dados de invocação de função e os metadados do gatilho (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Para obter um exemplo de metadados de gatilho, confira este [exemplo de hubs de eventos](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>Método context.done

```js
context.done([err],[propertyBag])
```

Permite que o runtime saiba que seu código foi concluído. Quando sua função usa a [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaração, você não precisa usar `context.done()` . O `context.done` retorno de chamada é chamado implicitamente. As funções assíncronas estão disponíveis no Nó 8 ou em uma versão posterior, que requer a versão 2.x do runtime de Funções.

Se sua função não for uma função assíncrona, **você deverá chamar** `context.done` para informar o tempo de execução de que sua função está concluída. Os tempos de execução se está ausente.

O método `context.done` permite que você retorne um erro definido pelo usuário ao runtime e um objeto JSON que contém dados de associação de saída. As propriedades transmitidas para `context.done`sobrescrevem qualquer coisa definida no objeto`context.bindings`.

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

Permite que você grave nos logs da função de streaming no nível de rastreamento padrão, com outros níveis de log disponíveis. O log de rastreamento é descrito em detalhes na próxima seção. 

## <a name="write-trace-output-to-logs"></a>Gravar saída de rastreamento em logs

No functions, você usa os `context.log` métodos para gravar a saída do rastreamento nos logs e no console do. Quando você chama `context.log()` , sua mensagem é gravada nos logs no nível de rastreamento padrão, que é o nível de rastreamento de _informações_ . As funções integram-se com o Aplicativo Azure insights para capturar melhor os logs do aplicativo de funções. Application Insights, parte do Azure Monitor, fornece recursos para coleta, renderização visual e análise da telemetria do aplicativo e de suas saídas de rastreamento. Para saber mais, consulte [monitoramento Azure Functions](functions-monitoring.md).

O exemplo a seguir grava um log no nível de rastreamento de informações, incluindo a ID de invocação:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

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

> [!NOTE]  
> Não use `console.log` para gravar saídas de rastreamento. Como a saída de `console.log` é capturada no nível do aplicativo de funções, ela não está vinculada a uma invocação de função específica e não é exibida em logs de uma função específica. Além disso, a versão 1. x do tempo de execução do Functions não dá suporte ao uso do `console.log` para gravar no console.

### <a name="trace-levels"></a>Níveis de rastreamento

Além do nível padrão, os seguintes métodos de log estão disponíveis para permitir que você grave logs de função em níveis de rastreamento específicos.

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **erro (_mensagem_)**   | Grava um evento de nível de erro nos logs.   |
| **warn(_message_)**    | Grava um evento no nível de aviso nos logs. |
| **informações (_mensagem_)**    | Grava no registro em log no nível da informação, ou em um nível inferior.    |
| **verbose(_message_)** | Grava no registro em log no nível detalhado.           |

O exemplo a seguir grava o mesmo log no nível de rastreamento de aviso, em vez do nível de informações:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Como _erro_ é o nível de rastreamento mais alto, esse rastreamento é gravado na saída em todos os níveis de rastreamento enquanto o registro em log estiver habilitado.

### <a name="configure-the-trace-level-for-logging"></a>Configurar o nível de rastreamento para registro em log

O Functions permite que você defina o nível de rastreamento de limite para gravação nos logs ou no console do. As configurações de limite específicas dependem da sua versão do tempo de execução do functions.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Para definir o limite para rastreamentos gravados nos logs, use a `logging.logLevel` propriedade no host.jsno arquivo. Esse objeto JSON permite que você defina um limite padrão para todas as funções em seu aplicativo de funções, além de poder definir limites específicos para funções individuais. Para saber mais, consulte [como configurar o monitoramento para Azure Functions](configure-monitoring.md).

# <a name="v1x"></a>[v1. x](#tab/v1)

Para definir o limite para todos os rastreamentos gravados nos logs e o console, use a `tracing.consoleLevel` propriedade no host.jsno arquivo. Essa configuração se aplica a todas as funções em seu aplicativo de função. O exemplo a seguir define o limite de rastreamento para habilitar o registro em log detalhado:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Os valores de **consoleLevel** correspondem aos nomes dos métodos `context.log`. Para desabilitar todo o registro em log do rastreamento no console, defina **consoleLevel** como _off_. Para obter mais informações, consulte [host.jsna referência v1. x](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Registrar telemetria personalizada

Por padrão, o Functions grava saída como rastreamentos para Application Insights. Para obter mais controle, você pode usar o [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) para enviar dados de telemetria personalizados para sua instância de Application insights. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1. x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

O parâmetro `tagOverrides` define `operation_Id` para a ID de invocação de função. Essa configuração permite que você correlacione toda a telemetria gerada automaticamente e a telemetria personalizada para uma dada invocação de função.

## <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

HTTP e gatilhos de webhook e associações de saída HTTP usam objetos de solicitação e resposta para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto da solicitação

O objeto `context.req` (solicitação) tem as seguintes propriedades:

| Propriedade      | Descrição                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Um objeto que contém o corpo da solicitação.               |
| _conector_     | Um objeto que contém os cabeçalhos da solicitação.                   |
| _forma_      | O método HTTP da solicitação.                                |
| _originalUrl_ | A URL da solicitação.                                        |
| _params_      | Um objeto que contém os parâmetros de roteamento da solicitação. |
| _consulta_       | Um objeto que contém os parâmetros da consulta.                  |
| _rawBody_     | O corpo da mensagem como uma cadeia de caracteres.                           |


### <a name="response-object"></a>Objeto de resposta

O objeto `context.res` (resposta) tem as seguintes propriedades:

| Propriedade  | Descrição                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Um objeto que contém o corpo da resposta.         |
| _conector_ | Um objeto que contém os cabeçalhos da resposta.             |
| _isRaw_   | Indica que a formatação foi ignorada para a resposta.    |
| _status_  | O código de status HTTP da resposta.                     |
| _arar_ | Uma matriz de objetos de cookie HTTP que são definidos na resposta. Um objeto de cookie HTTP tem uma `name` , `value` e outras propriedades de cookie, como `maxAge` ou `sameSite` . |

### <a name="accessing-the-request-and-response"></a>Acessar a solicitação e a resposta 

Ao trabalhar com gatilhos HTTP, há várias maneiras de acessar os objetos de solicitação e resposta HTTP:

+ **Partir `req` e `res` propriedades no `context` objeto.** Dessa forma, você pode usar o padrão convencional para acessar os dados HTTP a partir do objeto de contexto, em vez de usar o padrão `context.bindings.name` completo. O exemplo a seguir mostra como acessar os objetos `req` e `res` no `context`:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **A partir das associações de entrada e saída nomeadas.** Dessa forma, o gatilho e as associações de HTTP funcionam da mesma forma que qualquer outra associação. O exemplo a seguir define o objeto de resposta usando uma associação chamada `response`: 

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
+ **_[Somente Resposta]_ Chamando`context.res.send(body?: any)`.** Uma resposta HTTP é criada com a entrada `body` como o corpo da resposta. `context.done()` é chamado implicitamente.

+ **_[Somente Resposta]_ Chamando`context.done()`.** Um tipo especial de associação HTTP retorna a resposta que é passada para o `context.done()` método. A seguinte associação de saída HTTP define um parâmetro de saída `$return`:

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

## <a name="scaling-and-concurrency"></a>Dimensionamento e simultaneidade

Por padrão, o Azure Functions monitora automaticamente a carga em seu aplicativo e cria instâncias de host adicionais para Node.js conforme necessário. O Functions usa limites internos (não configuráveis pelo usuário) em tipos de gatilhos diferentes para decidir quando adicionar instâncias, por exemplo, a idade das mensagens e o tamanho da fila para QueueTrigger. Para obter mais informações, confira [Como funcionam os planos de consumo e Premium](event-driven-scaling.md).

Esse comportamento de dimensionamento é suficiente para muitos aplicativos Node.js. Para aplicativos associados à CPU, você pode melhorar ainda mais o desempenho usando vários processos de trabalho de linguagem.

Por padrão, cada instância de host do Functions tem um processo de trabalho de linguagem único. Você pode aumentar o número de processos de trabalho por host (até 10) com a configuração de aplicativo [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). O Azure Functions tenta distribuir uniformemente invocações de função simultâneas entre esses trabalhos. 

O FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que o Functions cria quando escala horizontalmente seu aplicativo para atender à demanda. 

## <a name="node-version"></a>Versão do nó

A tabela a seguir mostra as versões de Node.js com suporte atuais para cada versão principal do tempo de execução do functions, por sistema operacional:

| Versão do Functions | Versão do nó (Windows) | Versão do nó (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (bloqueada pelo runtime) | n/a |
| 2. x  | `~8`<br/>`~10` aconselhável<br/>`~12` | `node|8`<br/>`node|10` aconselhável  |
| 3.x | `~10`<br/>`~12` aconselhável<br/>`~14` (versão prévia)  | `node|10`<br/>`node|12` aconselhável<br/>`node|14` (versão prévia) |

Você pode ver a versão atual que o tempo de execução está usando registrando-se `process.version` em qualquer função.

### <a name="setting-the-node-version"></a>Configurando a versão do nó

Para aplicativos de funções do Windows, direcione a versão no Azure definindo a `WEBSITE_NODE_DEFAULT_VERSION` [configuração do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) como uma versão do LTS com suporte, como `~12` .

Para aplicativos de funções do Linux, execute o seguinte comando CLI do Azure para atualizar a versão do nó.

```bash
az functionapp config set --linux-fx-version "node|12" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

## <a name="dependency-management"></a>Gerenciamento de dependência
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
1. Ir para `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **console de depuração**  >  **cmd**.

3. Acesse `D:\home\site\wwwroot`e arraste o arquivo package.json para a pasta **wwwroot** na metade superior da página.  
    Também há outras maneiras de carregar arquivos em seu aplicativo de função. Para saber mais, confira [Como atualizar os arquivos do aplicativo de função](functions-reference.md#fileupdate). 

4. Depois que o arquivo package.json é carregado, execute o comando `npm install` no **console de execução remota do Kudu**.  
    Essa ação baixa os pacotes indicados no arquivo package.json e reinicia o aplicativo de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Adicione suas próprias variáveis de ambiente a um aplicativo de funções, em seus ambientes locais e de nuvem, como segredos operacionais (cadeias de conexão, chaves e pontos de extremidade) ou configurações ambientais (como variáveis de criação de perfil). Acesse essas configurações usando `process.env` o no seu código de função.

### <a name="in-local-development-environment"></a>No ambiente de desenvolvimento local

Ao executar localmente, seu projeto de funções inclui um [ `local.settings.json` arquivo](./functions-run-local.md), onde você armazena suas variáveis de ambiente no `Values` objeto. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>No ambiente de nuvem do Azure

Ao executar no Azure, o aplicativo de funções permite que você defina o usa [configurações do aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, e expõe essas configurações como variáveis de ambiente durante a execução. 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>Acessar variáveis de ambiente no código

Acesse as configurações do aplicativo como variáveis de ambiente usando `process.env` , conforme mostrado aqui na segunda e terceira chamadas para `context.log()` onde registramos as `AzureWebJobsStorage` variáveis de `WEBSITE_SITE_NAME` ambiente e:

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="configure-function-entry-point"></a>Configurar o ponto de entrada de função

As propriedades `scriptFile` e `entryPoint` do `function.json` podem ser usadas para configurar o local e o nome da função exportada. Essas propriedades podem ser importantes quando seu JavaScript é transferido.

### <a name="using-scriptfile"></a>Usando `scriptFile`

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

### <a name="using-entrypoint"></a>Usando `entryPoint`

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

Neste exemplo, é importante observar que, embora um objeto esteja sendo exportado, não há nenhuma garantia para preservar o estado entre as execuções.

## <a name="local-debugging"></a>Depuração local

Quando iniciado com o `--inspect` parâmetro, um processo de Node.js escuta um cliente de depuração na porta especificada. No Azure Functions 2. x, você pode especificar argumentos para passar para o processo de Node.js que executa seu código, adicionando a variável de ambiente ou a configuração de aplicativo `languageWorkers:node:arguments = <args>` . 

Para depurar localmente, adicione `"languageWorkers:node:arguments": "--inspect=5858"` em `Values` em seu [local.settings.jsno](./functions-run-local.md#local-settings-file) arquivo e anexe um depurador à porta 5858.

Ao depurar usando VS Code, o `--inspect` parâmetro é adicionado automaticamente usando o `port` valor na launch.jsdo projeto no arquivo.

Na versão 1. x, a configuração `languageWorkers:node:arguments` não funcionará. A porta de depuração pode ser selecionada com o [`--nodeDebugPort`](./functions-run-local.md#start) parâmetro em Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Quando você visa a versão 2. x do tempo de execução do functions, ambos [Azure Functions para Visual Studio Code](./create-first-function-cli-typescript.md) e o [Azure Functions Core Tools](functions-run-local.md) permitem criar aplicativos de funções usando um modelo que ofereça suporte a projetos de aplicativo de função TypeScript. O modelo gera `package.json` e `tsconfig.json` arquivos de projeto que facilitam a transcompilação, execução e publicação de funções JavaScript do código TypeScript com essas ferramentas.

Um `.funcignore` arquivo gerado é usado para indicar quais arquivos são excluídos quando um projeto é publicado no Azure.  

Os arquivos TypeScript (. TS) são transcompilados em arquivos JavaScript (. js) no `dist` diretório de saída. Os modelos do TypeScript usam o [ `scriptFile` parâmetro](#using-scriptfile) no `function.json` para indicar o local do arquivo. js correspondente na `dist` pasta. O local de saída é definido pelo modelo usando o `outDir` parâmetro no `tsconfig.json` arquivo. Se você alterar essa configuração ou o nome da pasta, o tempo de execução não será capaz de localizar o código a ser executado.

A maneira como você desenvolve e implanta localmente a partir de um projeto TypeScript depende de sua ferramenta de desenvolvimento.

### <a name="visual-studio-code"></a>Visual Studio Code

O [Azure Functions para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extensão permite desenvolver suas funções usando o TypeScript. As ferramentas principais são um requisito da extensão de Azure Functions.

Para criar um aplicativo de função TypeScript no Visual Studio Code, escolha `TypeScript` como seu idioma ao criar um aplicativo de funções.

Quando você pressiona **F5** para executar o aplicativo localmente, o transpilação é feito antes que o host (func.exe) seja inicializado. 

Quando você implanta seu aplicativo de funções no Azure usando o botão **implantar no aplicativo de funções...** , a extensão Azure Functions primeiro gera uma compilação pronta para produção de arquivos JavaScript dos arquivos de origem do TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Há várias maneiras pelas quais um projeto TypeScript difere de um projeto JavaScript ao usar as ferramentas principais.

#### <a name="create-project"></a>Criar projeto

Para criar um projeto de aplicativo de função TypeScript usando ferramentas básicas, você deve especificar a opção de linguagem TypeScript ao criar seu aplicativo de funções. Você pode fazer isso de uma das seguintes maneiras:

- Execute o `func init` comando, selecione `node` como sua pilha de idiomas e, em seguida, selecione `typescript` .

- Execute o comando `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Executar local

Para executar o código do aplicativo de funções localmente usando as ferramentas básicas, use os seguintes comandos em vez de `func host start` : 

```command
npm install
npm start
```

O `npm start` comando é equivalente aos seguintes comandos:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publicar no Azure

Antes de usar o [`func azure functionapp publish`] comando para implantar no Azure, você cria uma compilação de arquivos JavaScript pronta para produção a partir dos arquivos de origem do TypeScript. 

Os comandos a seguir preparam e publicam seu projeto TypeScript usando ferramentas básicas: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Neste comando, substitua `<APP_NAME>` pelo nome do seu aplicativo de funções.

## <a name="considerations-for-javascript-functions"></a>Considerações para funções em JavaScript

Ao trabalhar com funções JavaScript, lembre-se das considerações nas seções a seguir.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolher Planos do Serviço de Aplicativo de vCPU único

Ao criar um aplicativo de funções que usa o Plano do Serviço de Aplicativo, recomendamos que você selecione um plano de vCPU único em vez de um plano com vários vCPUs. Atualmente, o Functions executa funções em JavaScript com mais eficiência em VMs de vCPU único, e o uso de VMs maiores não produz os aprimoramentos de desempenho esperados. Quando necessário, você pode escalar horizontalmente manualmente Adicionando mais instâncias de VM de vCPU único ou pode habilitar o dimensionamento automático. Para saber mais, confira [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Inicialização a frio

No desenvolvimento de Azure Functions no modelo de hospedagem sem servidor, as inicializações a frio são uma realidade. *Partida a frio* refere-se ao fato de que, quando seu aplicativo de função é iniciado pela primeira vez após um período de inatividade, leva mais tempo para inicializar. Para funções JavaScript com árvores de dependência grandes em particular, o cold start pode ser significativo. Para acelerar o processo de inicialização a frio, [execute suas funções como um arquivo de pacote](run-functions-from-deployment-package.md) quando possível. Muitos métodos de implantação usam a execução do modelo de pacote por padrão, mas se você estiver experimentando grandes reinicializações a frio e não estiver sendo executado dessa maneira, essa alteração poderá oferecer uma melhoria significativa.

### <a name="connection-limits"></a>Limites de conexão

Quando você usa um cliente específico do serviço em um aplicativo Azure Functions, não crie um novo cliente com cada invocação de função. Em vez disso, crie um único cliente estático no escopo global. Para obter mais informações, consulte [Managing Connections in Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Usar `async` e `await`

Ao escrever Azure Functions em JavaScript, você deve escrever código usando as `async` `await` palavras-chave e. Escrever código usando `async` e `await` em vez de retornos de chamada ou `.then` `.catch` com promessas ajuda a evitar dois problemas comuns:
 - Lançar exceções não capturadas que [falham no processo de Node.js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potencialmente afetando a execução de outras funções.
 - Comportamento inesperado, como logs ausentes de Context. log, causados por chamadas assíncronas que não estão aguardando corretamente.

No exemplo a seguir, o método assíncrono `fs.readFile` é invocado com uma função de retorno de chamada de erro-primeiro como seu segundo parâmetro. Esse código causa os dois problemas mencionados acima. Uma exceção que não é detectada explicitamente no escopo correto falha em todo o processo (problema #1). Chamar `context.done()` fora do escopo da função de retorno de chamada significa que a invocação de função pode terminar antes que o arquivo seja lido (problema #2). Neste exemplo, chamar `context.done()` resultados muito antigos em entradas de log ausentes começando com `Data from file:` .

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

Usar as `async` `await` palavras-chave e ajuda a evitar esses dois erros. Você deve usar a função utilitário Node.js [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) para ativar as funções de estilo de retorno de chamada de erro primeiro em funções awaitable.

No exemplo a seguir, todas as exceções não tratadas lançadas durante a execução da função falham apenas na invocação individual que gerou uma exceção. A `await` palavra-chave significa que as etapas a seguir `readFileAsync` são executadas somente após a `readFile` conclusão. Com `async` `await` o e o, você também não precisa chamar o retorno de chamada `context.done()` .

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
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

[' Func functionapp publish ' do Azure]: functions-run-local.md#project-file-deployment