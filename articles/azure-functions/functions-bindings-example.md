---
title: Exemplo de gatilho e associação de funções do Azure
description: Saiba como configurar associações de função do Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437881"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Exemplo de gatilho e associação de funções do Azure

Este artigo demonstra como configurar uma [gatilho e associações](./functions-triggers-bindings.md) em uma função do Azure.

Suponha que você deseja gravar uma nova linha no Armazenamento de Tabelas do Azure sempre que uma nova mensagem aparece no Armazenamento de Filas do Azure. Esse cenário pode ser implementado usando um gatilho do Armazenamento de Filas do Azure e uma associação de saída do Armazenamento de Tabelas do Azure. 

Aqui está um arquivo *function.json* para esse cenário. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

O primeiro elemento na matriz `bindings` é o gatilho do Armazenamento de Filas. As propriedades `type` e `direction` identificam o gatilho. A propriedade `name` identifica o parâmetro de função que recebe o conteúdo da mensagem de fila. O nome da fila a ser monitorada está em `queueName` e a cadeia de conexão está na configuração de aplicativo identificada por `connection`.

O segundo elemento na matriz `bindings` é a associação de saída do Armazenamento de Tabelas do Azure. As propriedades `type` e `direction` identificam a associação. A propriedade `name` especifica como a função fornece a nova linha da tabela, nesse caso, usando o valor retornado da função. O nome da tabela está em `tableName` e a cadeia de conexão está na configuração de aplicativo identificada por `connection`.

Para exibir e editar o conteúdo de *function.json* no Portal do Azure, clique na opção **Editor avançado** na guia **Integrar** da sua função.

> [!NOTE]
> O valor de `connection` é o nome de uma configuração de aplicativo que contém a cadeia de conexão, não a cadeia de conexão propriamente dita. Associações usam cadeias de conexão armazenadas em configurações de aplicativo para impor a melhor prática que dita que *function.json* não contêm segredos do serviço.

## <a name="c-script-example"></a>Exemplo 2 de C# script

Aqui está o código de script C# que funciona com esse gatilho e essa associação. Observe que o nome do parâmetro que fornece o conteúdo da mensagem da fila é `order`; esse nome é necessário porque o valor da propriedade `name` em *function.json* é `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Exemplo de JavaScript

O mesmo *Function. JSON* arquivo pode ser usado com uma função JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Exemplo da biblioteca de classe

Em uma biblioteca de classes, o mesmo gatilho e informações de associação &mdash; nomes de fila e tabela, contas de armazenamento, parâmetros de função para entrada e saída &mdash; é fornecido por atributos em vez de um arquivo function.json. Aqui está um exemplo:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Agora você tem uma função de trabalho que é disparada pelo armazenamento de tabelas do Azure que envia dados para uma fila.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Padrões de expressões de associação de funções do Azure](./functions-bindings-expressions-patterns.md)

