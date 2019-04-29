---
title: Usando o valor de retorno de uma função do Azure
description: Saiba como gerenciar os valores de retorno para o Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 01/14/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437711"
---
# <a name="using-the-azure-function-return-value"></a>Usando o valor de retorno de função do Azure

Este artigo explica como os valores de retorno de trabalho dentro de uma função.

Em linguagens que têm um valor de retorno, você pode associar uma função [associação de saída](./functions-triggers-bindings.md#binding-direction) para o valor de retorno:

- Em uma biblioteca de classe C#, aplique o atributo de associação de saída para o valor de retorno do método.
- Em outras linguagens, defina a propriedade `name` em *function.json* para `$return`.

Se houver várias associações de saída, use o valor de retorno de apenas um deles.

No C# e script C#, formas alternativas de enviar os dados para uma associação de saída são parâmetros `out` e [objetos de coletor](functions-reference-csharp.md#writing-multiple-output-values).

Veja o exemplo específico de uma linguagem que mostra o uso do valor de retorno:

- [C#](#c-example)
- [Script do C# (.csx)](#c-script-example)
- [F#](#f-example)
- [JavaScript](#javascript-example)
- [Python](#python-example)

## <a name="c-example"></a>Exemplo de C#

Aqui está o código C# que usa o valor de retorno para uma associação de saída, seguido por um exemplo de assíncrono:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="c-script-example"></a>Exemplo 2 de C# script

Aqui está a associação de saída no arquivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Aqui está o código de script C#, seguido por um exemplo de assíncrono:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="f-example"></a>Exemplo de F#

Aqui está a associação de saída no arquivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

O código F# é o seguinte:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>Exemplo de JavaScript

Aqui está a associação de saída no arquivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

No JavaScript, o valor de retorno fica no segundo parâmetro para `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Exemplo de Python

Aqui está a associação de saída no arquivo *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Aqui está o código Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tratar erros de associação de funções do Azure](./functions-bindings-errors.md)

