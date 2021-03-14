---
title: Azure Functions manipuladores personalizados
description: Aprenda a usar Azure Functions com qualquer linguagem ou versão de tempo de execução.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: dd112c74ea9f013a0e14bddd735060ddbf73c14e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578525"
---
# <a name="azure-functions-custom-handlers"></a>Azure Functions manipuladores personalizados

Cada aplicativo de funções é executado por um manipulador específico de idioma. Embora Azure Functions ofereça suporte a muitos [manipuladores de idioma](./supported-languages.md) por padrão, há casos em que você talvez queira usar outros idiomas ou tempos de execução.

Os manipuladores personalizados são servidores Web leves que recebem eventos do host do functions. Qualquer linguagem que ofereça suporte a primitivos HTTP pode implementar um manipulador personalizado.

Os manipuladores personalizados são mais adequados para situações em que você deseja:

- Implemente um aplicativo de funções em uma linguagem que não tem suporte atualmente, como Go ou Rust.
- Implemente um aplicativo de funções em um tempo de execução que não tem suporte atualmente, como deno.

Com manipuladores personalizados, você pode usar [gatilhos e associações de entrada e saída](./functions-triggers-bindings.md) por meio de [pacotes de extensão](./functions-bindings-register.md).

Comece a usar Azure Functions manipuladores personalizados com os [guias de início rápido em Go e Rust](create-first-function-vs-code-other.md).

## <a name="overview"></a>Visão geral

O diagrama a seguir mostra a relação entre o host do Functions e um servidor Web implementado como um manipulador personalizado.

![Visão geral do manipulador personalizado de Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Cada evento dispara uma solicitação enviada ao host do functions. Um evento é qualquer disparador com suporte pelo Azure Functions.
1. O host do Functions emite, então, uma [carga de solicitação](#request-payload) para o servidor Web. A carga contém dados de associação de entrada e de gatilho e outros metadados para a função.
1. O servidor Web executa a função individual e retorna uma carga de [resposta](#response-payload) para o host de funções.
1. O host de funções passa dados da resposta para as associações de saída da função para processamento.

Um aplicativo Azure Functions implementado como um manipulador personalizado deve configurar o *host.jsno*, *local.settings.jsem* e *function.jsem* arquivos de acordo com algumas convenções.

## <a name="application-structure"></a>Estrutura de aplicativo

Para implementar um manipulador personalizado, você precisa dos seguintes aspectos para seu aplicativo:

- Um *host.jsno* arquivo na raiz do seu aplicativo
- Um *local.settings.jsno* arquivo na raiz do seu aplicativo
- Um *function.jsno* arquivo para cada função (dentro de uma pasta que corresponde ao nome da função)
- Um comando, script ou executável, que executa um servidor Web

O diagrama a seguir mostra como esses arquivos examinam o sistema de arquivos em busca de uma função chamada "MyQueueFunction" e um executável de manipulador personalizado chamado *handler.exe*.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Configuração

O aplicativo é configurado por meio do *host.jsno* e *local.settings.jsem* arquivos.

#### <a name="hostjson"></a>host. JSON

*host.json* informa ao host do Functions onde enviar solicitações apontando para um servidor Web capaz de processar eventos http.

Um manipulador personalizado é definido com a configuração do *host.jsno* arquivo com detalhes sobre como executar o servidor Web por meio da `customHandler` seção.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

A `customHandler` seção aponta para um destino, conforme definido pelo `defaultExecutablePath` . O destino de execução pode ser um comando, executável ou arquivo no qual o servidor Web é implementado.

Use a `arguments` matriz para passar quaisquer argumentos para o executável. Os argumentos dão suporte à expansão de variáveis de ambiente (configurações do aplicativo) usando a `%%` notação.

Você também pode alterar o diretório de trabalho usado pelo executável com `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Suporte a associações

Os gatilhos padrão juntamente com as associações de entrada e saída estão disponíveis por meio da referência de [pacotes de extensão](./functions-bindings-register.md) em seu *host.jsno* arquivo.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.js* define as configurações de aplicativo usadas ao executar o aplicativo de funções localmente. Como ele pode conter segredos, *local.settings.jsem* deve ser excluído do controle do código-fonte. No Azure, use as configurações de aplicativo em vez disso.

Para manipuladores personalizados, defina `FUNCTIONS_WORKER_RUNTIME` como `Custom` em *local.settings.jsem*.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>Metadados de função

Quando usado com um manipulador personalizado, o *function.jsno* conteúdo não é diferente de como você definiria uma função em qualquer outro contexto. O único requisito é que *function.jsem* arquivos deve estar em uma pasta chamada para corresponder ao nome da função.

O *function.js* a seguir configura uma função que tem um gatilho de fila e uma associação de saída de fila. Como ele está em uma pasta chamada *MyQueueFunction*, ele define uma função chamada *MyQueueFunction*.

**MyQueueFunction/function.jsem**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Carga de solicitação

Quando uma mensagem da fila é recebida, o host do Functions envia uma solicitação HTTP post para o manipulador personalizado com uma carga no corpo.

O código a seguir representa um exemplo de carga de solicitação. A carga inclui uma estrutura JSON com dois membros: `Data` e `Metadata` .

O `Data` membro inclui chaves que correspondem a nomes de entrada e de gatilho conforme definido na matriz bindings no *function.jsno* arquivo.

O `Metadata` membro inclui [metadados gerados da origem do evento](./functions-bindings-expressions-patterns.md#trigger-metadata).

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Carga de resposta

Por convenção, as respostas de função são formatadas como pares de chave/valor. As chaves com suporte incluem:

| <nobr>Chave de carga</nobr>   | Tipo de dados | Comentários                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | Mantém valores de resposta conforme definidos pela `bindings` matriz no *function.jsem*.<br /><br />Por exemplo, se uma função for configurada com uma associação de saída de fila chamada "myQueueOutput", `Outputs` conterá uma chave chamada `myQueueOutput` , que é definida pelo manipulador personalizado para as mensagens que são enviadas para a fila. |
| `Logs`        | array     | As mensagens aparecem nos logs de invocação de funções.<br /><br />Ao executar no Azure, as mensagens aparecem no Application Insights. |
| `ReturnValue` | string    | Usado para fornecer uma resposta quando uma saída é configurada como `$return` na *function.jsno* arquivo. |

Este é um exemplo de uma carga de resposta.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Exemplos

Os manipuladores personalizados podem ser implementados em qualquer linguagem que dê suporte ao recebimento de eventos HTTP. Os exemplos a seguir mostram como implementar um manipulador personalizado usando a linguagem de programação go.

### <a name="function-with-bindings"></a>Função com associações

O cenário implementado neste exemplo apresenta uma função chamada `order` que aceita um `POST` com uma carga que representa uma ordem de produto. Como um pedido é Postado na função, uma mensagem de armazenamento de fila é criada e uma resposta HTTP é retornada.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementação

Em uma pasta chamada *Order*, o *function.jsno* arquivo configura a função disparada por http.

**ordenar/function.jsem**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Essa função é definida como uma [função disparada por http](./functions-bindings-http-webhook-trigger.md) que retorna uma [resposta http](./functions-bindings-http-webhook-output.md) e gera uma mensagem de [armazenamento de fila](./functions-bindings-storage-queue-output.md) .

Na raiz do aplicativo, a *host.jsno* arquivo é configurada para executar um arquivo executável chamado `handler.exe` ( `handler` no Linux ou MacOS).

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Essa é a solicitação HTTP enviada ao tempo de execução do functions.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

Em seguida, o tempo de execução do Functions enviará a seguinte solicitação HTTP para o manipulador personalizado:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Algumas partes da carga foram removidas para fins de brevidade.

*handler.exe* é o programa de manipulador personalizado go compilado que executa um servidor Web e responde a solicitações de invocação de função do host do functions.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Neste exemplo, o manipulador personalizado executa um servidor Web para manipular eventos HTTP e é definido para escutar solicitações por meio do `FUNCTIONS_CUSTOMHANDLER_PORT` .

Embora o host de funções receba a solicitação HTTP original em `/api/order` , ele invoca o manipulador personalizado usando o nome da função (seu nome de pasta). Neste exemplo, a função é definida no caminho de `/order` . O host envia ao manipulador personalizado uma solicitação HTTP no caminho de `/order` .

À medida que `POST` as solicitações são enviadas para essa função, os dados de gatilho e os metadados de função estão disponíveis por meio do corpo da solicitação HTTP. O corpo da solicitação HTTP original pode ser acessado na carga `Data.req.Body` .

A resposta da função é formatada em pares de chave/valor em que o `Outputs` membro mantém um valor JSON em que as chaves correspondem às saídas, conforme definido no *function.jsno* arquivo.

Este é um exemplo de carga que esse manipulador retorna para o host do functions.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Ao definir a `message` saída igual aos dados de pedidos que vieram da solicitação, a função gera esses dados de pedidos para a fila configurada. O host do Functions também retorna a resposta HTTP configurada em `res` para o chamador.

### <a name="http-only-function"></a>Função somente HTTP

Para funções disparadas por HTTP sem associações ou saídas adicionais, talvez você queira que o manipulador trabalhe diretamente com a solicitação e resposta HTTP em vez de conteúdo de [solicitação](#request-payload) e [resposta](#response-payload) de manipulador personalizado. Esse comportamento pode ser configurado em *host.js* usando a `enableForwardingHttpRequest` configuração.

> [!IMPORTANT]
> A principal finalidade do recurso de manipuladores personalizados é habilitar linguagens e tempos de execução que atualmente não têm suporte de primeira classe no Azure Functions. Embora possa ser possível executar aplicativos Web usando manipuladores personalizados, Azure Functions não é um proxy reverso padrão. Alguns recursos, como transmissão de resposta, HTTP/2 e WebSockets, não estão disponíveis. Alguns componentes da solicitação HTTP, como determinados cabeçalhos e rotas, podem ser restritos. Seu aplicativo também pode experimentar o [início frio](event-driven-scaling.md#cold-start)excessivo.
>
> Para tratar dessas circunstâncias, considere a execução de seus aplicativos Web no [serviço Azure app](../app-service/overview.md).

O exemplo a seguir demonstra como configurar uma função disparada por HTTP sem associações ou saídas adicionais. O cenário implementado neste exemplo apresenta uma função chamada `hello` que aceita um `GET` ou `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementação

Em uma pasta chamada *Hello*, o *function.jsno* arquivo configura a função disparada por http.

**Olá/function.jsem**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

A função é configurada para aceitar as `GET` solicitações e e `POST` o valor do resultado é fornecido por meio de um argumento chamado `res` .

Na raiz do aplicativo, a *host.jsno* arquivo é configurada para ser executada `handler.exe` e `enableForwardingHttpRequest` é definida como `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Quando `enableForwardingHttpRequest` é `true` , o comportamento das funções somente http difere do comportamento padrão dos manipuladores personalizados das seguintes maneiras:

* A solicitação HTTP não contém a carga de [solicitação](#request-payload) de manipuladores personalizados. Em vez disso, o host do Functions invoca o manipulador com uma cópia da solicitação HTTP original.
* O host do Functions invoca o manipulador com o mesmo caminho da solicitação original, incluindo qualquer parâmetro de cadeia de caracteres de consulta.
* O host do Functions retorna uma cópia da resposta HTTP do manipulador como a resposta à solicitação original.

A seguir está uma solicitação POST para o host do functions. O host do Functions envia uma cópia da solicitação para o manipulador personalizado no mesmo caminho.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

O arquivo *. go do manipulador* de arquivos implementa um servidor Web e uma função http.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Neste exemplo, o manipulador personalizado cria um servidor Web para manipular eventos HTTP e é definido para escutar solicitações por meio do `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` as solicitações são tratadas retornando uma cadeia de caracteres e `POST` as solicitações têm acesso ao corpo da solicitação.

A rota para a função Order aqui é `/api/hello` , igual à solicitação original.

>[!NOTE]
>O `FUNCTIONS_CUSTOMHANDLER_PORT` não é a porta voltada para o público usada para chamar a função. Essa porta é usada pelo host do Functions para chamar o manipulador personalizado.

## <a name="deploying"></a>Implantando

Um manipulador personalizado pode ser implantado em cada opção de Hospedagem de Azure Functions. Se o seu manipulador exigir dependências do sistema operacional ou da plataforma (como um tempo de execução de linguagem), talvez seja necessário usar um [contêiner personalizado](./functions-create-function-linux-custom-image.md).

Ao criar um aplicativo de funções no Azure para manipuladores personalizados, recomendamos que você selecione .NET Core como a pilha. Uma pilha "personalizada" para manipuladores personalizados será adicionada no futuro.

Para implantar um aplicativo de manipulador personalizado usando Azure Functions Core Tools, execute o comando a seguir.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Verifique se todos os arquivos necessários para executar o manipulador personalizado estão na pasta e estão incluídos na implantação. Se o seu manipulador personalizado for um executável binário ou tiver dependências específicas da plataforma, verifique se esses arquivos correspondem à plataforma de implantação de destino.

## <a name="restrictions"></a>Restrições

- O servidor Web do manipulador personalizado precisa começar dentro de 60 segundos.

## <a name="samples"></a>Exemplos

Consulte o [repositório do GitHub de exemplos de manipulador personalizado](https://github.com/Azure-Samples/functions-custom-handlers) para obter exemplos de como implementar funções em uma variedade de idiomas diferentes.

## <a name="troubleshooting-and-support"></a>Solução de problemas e suporte

### <a name="trace-logging"></a>Log de rastreamento

Se o processo do manipulador personalizado falhar ao iniciar ou se ele tiver problemas de comunicação com o host do functions, você poderá aumentar o nível de log do aplicativo de funções para `Trace` para ver mais mensagens de diagnóstico do host.

Para alterar o nível de log padrão do aplicativo de funções, defina a `logLevel` configuração na `logging` seção de *host.jsem*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

O host do Functions gera mensagens de log extras, incluindo informações relacionadas ao processo do manipulador personalizado. Use os logs para investigar problemas que iniciam o processo de manipulador personalizado ou invocando funções em seu manipulador personalizado.

Localmente, os logs são impressos no console do.

No Azure, os [rastreamentos de Application insights de consulta](analyze-telemetry-data.md#query-telemetry-data) para exibir as mensagens de log. Se seu aplicativo produz um alto volume de logs, apenas um subconjunto de mensagens de log é enviado para Application Insights. [Desabilite a amostragem](configure-monitoring.md#configure-sampling) para garantir que todas as mensagens sejam registradas.

### <a name="test-custom-handler-in-isolation"></a>Testar manipulador personalizado em isolamento

Os aplicativos de manipulador personalizado são um processo de servidor Web, portanto, pode ser útil iniciá-lo por conta própria e testar invocações de função enviando [solicitações HTTP](#request-payload) fictícias usando uma ferramenta como [ondulação](https://curl.haxx.se/) ou [postmaster](https://www.postman.com/).

Você também pode usar essa estratégia em seus pipelines de CI/CD para executar testes automatizados em seu manipulador personalizado.

### <a name="execution-environment"></a>Ambiente de execução

Os manipuladores personalizados são executados no mesmo ambiente que um aplicativo Azure Functions típico. Teste seu manipulador para garantir que o ambiente contenha todas as dependências necessárias para executar. Para aplicativos que exigem dependências adicionais, talvez seja necessário executá-los usando uma [imagem de contêiner personalizada](functions-create-function-linux-custom-image.md) hospedada no [plano Azure Functions Premium](functions-premium-plan.md).

### <a name="get-support"></a>Obter suporte

Se precisar de ajuda em um aplicativo de funções com manipuladores personalizados, você poderá enviar uma solicitação por meio de canais de suporte regular. No entanto, devido à grande variedade de linguagens possíveis usadas para criar aplicativos de manipuladores personalizados, o suporte não é ilimitado.

O suporte estará disponível se o host do Functions tiver problemas para iniciar ou se comunicar com o processo do manipulador personalizado. Para problemas específicos para o funcionamento interno de seu processo de manipulador personalizado, como problemas com a linguagem ou estrutura escolhida, nossa equipe de suporte não pode fornecer assistência nesse contexto.

## <a name="next-steps"></a>Próximas etapas

Comece a criar um aplicativo Azure Functions em go ou Rust com o guia de [início rápido dos manipuladores personalizados](create-first-function-vs-code-other.md).
