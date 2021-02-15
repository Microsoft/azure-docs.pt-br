---
title: Mudando de Express.js para Azure Functions
description: Saiba como refatorar Express.js pontos de extremidade para Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810217"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Mudando de Express.js para Azure Functions

Express.js é uma das estruturas de Node.js mais populares para desenvolvedores da Web e continua sendo uma opção excelente para a criação de aplicativos que atendem aos pontos de extremidade da API.

Ao migrar o código para uma arquitetura sem servidor, a refatoração Express.js pontos de extremidade afeta as seguintes áreas:

- **Middleware**: o Express.js apresenta uma coleção robusta de middleware. Muitos módulos de middleware não são mais necessários na luz de Azure Functions e recursos de [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) . Verifique se você pode replicar ou substituir qualquer lógica manipulada pelo middleware essencial antes de migrar os pontos de extremidade.

- **APIs diferentes**: a API usada para processar as solicitações e as respostas difere entre Azure Functions e Express.js. O exemplo a seguir detalha as alterações necessárias.

- **Rota padrão**: por padrão, Azure Functions pontos de extremidade são expostos sob a `api` rota. As regras de roteamento são configuráveis por meio do [ `routePrefix` no _host.jsno_ arquivo](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Configuração e convenções**: um aplicativo de funções usa o _function.jsno_ arquivo para definir verbos HTTP, definir políticas de segurança e pode configurar a [entrada e a saída](./functions-triggers-bindings.md)da função. Por padrão, o nome da pasta que contém os arquivos de função define o nome do ponto de extremidade, mas você pode alterar o nome por meio da `route` Propriedade na [function.jsno](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) arquivo.

> [!TIP]
> Saiba mais por meio do [Refactor de tutorial interativo Node.js e APIs expressas para APIs sem servidor com Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/).

## <a name="example"></a>Exemplo

### <a name="expressjs"></a>Express.js

O exemplo a seguir mostra um ponto de extremidade de Express.js típico `GET` .

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

Quando uma `GET` solicitação é enviada para `/hello` , uma `HTTP 200` resposta que contém `Success` é retornada. Se o ponto de extremidade encontrar um erro, a resposta será um `HTTP 500` com os detalhes do erro.

### <a name="azure-functions"></a>Funções do Azure

Azure Functions organiza os arquivos de configuração e de código em uma única pasta para cada função. Por padrão, o nome da pasta determina o nome da função.

Por exemplo, uma função chamada `hello` tem uma pasta com os arquivos a seguir.

``` files
| - hello
|  - function.json
|  - index.js
```

O exemplo a seguir implementa o mesmo resultado que o ponto de extremidade de Express.js acima, mas com Azure Functions.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

Ao migrar para o functions, as seguintes alterações são feitas:

- **Módulo:** O código de função é implementado como um módulo JavaScript.

- **Objeto de contexto e resposta**: o [`context`](./functions-reference-node.md#context-object) permite que você se comunique com o tempo de execução da função. No contexto, você pode ler os dados da solicitação e definir a resposta da função. O código síncrono exige que você chame `context.done()` para concluir a execução, enquanto `asyc` as funções resolvem implicitamente a solicitação.

- **Convenção de nomenclatura**: o nome da pasta usada para conter os arquivos de Azure Functions é usado como o nome do ponto de extremidade por padrão (isso pode ser substituído no [function.jsem](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Configuração**: você define os verbos HTTP no [function.jsno](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) arquivo, como `POST` ou `PUT` .

Ofunction.jsa seguir _ no_ arquivo contém informações de configuração para a função.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Ao definir `get` na `methods` matriz, a função está disponível para solicitações HTTP `GET` . Se você quiser que sua API aceite solicitações de suporte `POST` , também poderá adicioná- `post` la à matriz.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais com o [Refactor de tutorial interativo Node.js e as APIs expressas para APIs sem servidor com Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/)