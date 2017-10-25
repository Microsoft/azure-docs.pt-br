---
title: "Referência do desenvolvedor de Java do Azure Functions | Microsoft Docs"
description: "Entenda como desenvolver funções usando Java."
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, java"
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/20/2017
ms.author: routlaw
ms.openlocfilehash: f8812c2e8ac3398dabd17feaf97897efca5566f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-java-developer-guide"></a>Guia do desenvolvedor de Java do Azure Functions
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>Modelo de programação 

Sua função do Azure deve ser um método de classe sem monitoração de estado que processa uma entrada e produz uma saída. Embora você tenha permissão para escrever métodos de instância, a função não deve depender de nenhum campo de instância da classe. Todos os métodos de função devem ter um modificador de acesso `public`.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

Normalmente, uma função do Azure é invocada devido a um gatilho externo. A função precisa processar esse gatilho as entradas associadas a ele e produzir uma ou mais saídas.

Anotações de Java estão incluídas no pacote `azure-functions-java-core` para associar a entrada e as saídas aos seus métodos. Os gatilhos de entrada com suporte e as anotações de associação de saída estão incluídos na tabela a seguir:

Associação | Anotação
---|---
CosmosDB | N/D
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Aplicativos Móveis | N/D
Hubs de Notificação | N/D
Blob de Armazenamento | <ul><li>`BlobTrigger`</li><li>`BlobOutput`</li><li>`BlobOutput`</li></ul>
Fila de Armazenamento | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tabela de Armazenamento | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Timer | <ul><li>`TimerTrigger`</li></ul>
Twilio | N/D

Entradas e saídas de gatilho também podem ser definidas no [function.json](/azure/azure-functions/functions-reference#function-code) de seu aplicativo.

> [!IMPORTANT] 
> Você precisa configurar uma conta de Armazenamento do Azure em seu [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) para executar gatilhos de Tabela, Fila ou Azure Storage Blob localmente.

Exemplo de uso de anotações:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

A mesma função escrita sem anotações:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

com o `function.json` correspondente:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>Tipos de dados

Você é livre para usar todos os tipos de dados em Java para os dados de entrada e saída, incluindo tipos nativos, tipos Java personalizados e tipos do Azure especializados definidos no pacote `azure-functions-java-core`. O tempo de execução do Azure Functions tenta converter a entrada recebida no tipo solicitado por seu código.

### <a name="strings"></a>Cadeias de caracteres

Os valores passados para os métodos de função serão convertidos em cadeias de caracteres se o tipo de parâmetro de entrada correspondente da função for `String`. 

### <a name="plain-old-java-objects-pojos"></a>POJOs (objetos Java básicos)

Cadeias de caracteres formatadas com JSON serão convertidas em tipos Java se a entrada do método de função esperar esse tipo de Java. Essa conversão permite passar entradas JSON para suas funções e trabalhar com tipos Java em seu código sem a necessidade de implementar a conversão em seu próprio código.

Tipos POJO usados como entradas de funções devem ter o mesmo modificador de acesso `public` que os métodos de função em que estão sendo usados. Você não precisa declarar campos de classe POJO `public`. Por exemplo, uma cadeia de caracteres JSON `{ "x": 3 }` pode ser convertida no tipo POJO a seguir:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Dados binários

Dados binários são representados como um `byte[]` em seu código de funções do Azure. Associe entradas ou saídas binárias a suas funções definindo o campo `dataType` em seu function.json como `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Em seguida, use-o em seu código de função:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Use o tipo `OutputBinding<byte[]>` para fazer uma associação de saída binária.


## <a name="function-method-overloading"></a>Sobrecarga de método de função

Você pode sobrecarregar métodos de função com o mesmo nome, mas com tipos diferentes. Por exemplo, você pode ter `String echo(String s)` e `String echo(MyType s)` em uma classe e o tempo de execução do Azure Functions decide qual invocar examinando o tipo de entrada real (para uma entrada HTTP, o tipo MIME `text/plain` leva a `String`, enquanto `application/json` representa `MyType`).

## <a name="inputs"></a>Entradas

As entradas são divididas em duas categorias no Azure Functions: uma é a entrada de gatilho e a outra é a entrada adicional. Embora elas sejam diferentes em `function.json`, seu uso é idêntico no código Java. Como exemplo, vejamos o trecho de código a seguir:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String in, @BindingName("item") MyObject obj) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

A anotação `@BindingName` aceita uma propriedade `String` que representa o nome da associação/gatilho definido em `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Sendo assim, quando essa função é invocada, a carga da solicitação HTTP passa um `String` para o argumento `in` e um tipo `MyObject` do Armazenamento de Tabelas do Azure é passado para o argumento `obj`.

## <a name="outputs"></a>outputs

As saídas podem ser expressas em parâmetros de saída ou em valores retornados. Se houver apenas uma saída, é recomendável usar o valor retornado. Para várias saídas, você precisa usar parâmetros de saída.

O valor retornado é a forma mais simples de saída, basta retornar o valor de qualquer tipo e o tempo de execução do Azure Functions tentará realizar seu marshaling de volta para o tipo real (como uma resposta HTTP). Em `functions.json`, você usa `$return` como o nome da associação de saída.

Para produzir vários valores de saída, use o tipo `OutputParameter<T>` definido no pacote `azure-functions-java-core`. Se precisar fazer uma resposta HTTP e enviar por push uma mensagem para uma fila, você poderá escrever algo como:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputParameter;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, @BindingName("message") OutputParameter<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

e definir a associação de saída em `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "myqueue",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Tipos especializados

Às vezes, uma função precisa ter controle detalhado sobre entradas e saídas. Tipos especializados no pacote `azure-functions-java-core` são fornecidos para manipular informações de solicitação e ajustar o status de retorno de um gatilho HTTP:

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage`  |    Gatilho de HTTP     | Obter método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Associação de saída HTTP | Retornar status diferentes de 200   |

> [!NOTE] 
> Você também pode usar a anotação `@BindingName` para obter cabeçalhos e consultas HTTP. Por exemplo, `@Bind("name") String query` itera os cabeçalhos e consultas de solicitação HTTP e passa esse valor para o método. Por exemplo, `query` será `"test"` se a URL de solicitação for `http://example.org/api/echo?name=test`.

## <a name="functions-execution-context"></a>Contexto de execução das funções

Você interage com o ambiente de execução do Azure Functions por meio do objeto `ExecutionContext` definido no pacote `azure-functions-java-core`. Use o objeto `ExecutionContext` para usar informações sobre invocação e informações sobre tempo de execução de funções em seu código.

### <a name="logging"></a>Registro em log

O acesso ao agente de tempo de execução das funções está disponível por meio do objeto `ExecutionContext`. Esse agente é vinculado ao Azure Monitor e permite sinalizar avisos e erros encontrados durante a execução da função.

O exemplo de código a seguir registra uma mensagem de aviso quando o corpo da solicitação recebida está vazio.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received in " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
