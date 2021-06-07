---
title: Associações de saída do armazenamento de tabelas do Azure para Azure Functions
description: Entenda como usar as associações de saída do armazenamento de tabelas do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f793f96f55a258b2d7cb11f214984416557618df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452995"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Associações de saída do armazenamento de tabelas do Azure para Azure Functions

Use uma associação de saída de armazenamento de Tabela do Azure para gravar entidades para uma tabela em uma conta de Armazenamento do Azure.

> [!NOTE]
> Essa associação de saída não dá suporte para atualização de entidades existentes. Use a `TableOperation.Replace` operação [do SDK do Armazenamento do Microsoft Azure](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) para atualizar uma entidade existente.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que usa um gatilho HTTP para gravar uma única linha de tabela.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que usa um gatilho HTTP para gravar uma única linha de tabela.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

O exemplo a seguir mostra uma função Java que usa um gatilho HTTP para gravar várias linhas da tabela.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-node.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como gravar várias entidades em uma tabela a partir de uma função.

Configuração de associação no _function.jsem_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Código do PowerShell no _run.ps1_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como usar a associação de saída de armazenamento de tabela. A `table` associação é configurada no *function.jsem* atribuindo valores a `name` , `tableName` , `partitionKey` e `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

A função a seguir gera um UUI exclusivo para o `rowKey` valor e persiste a mensagem no armazenamento de tabelas.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

O construtor do atributo usa o nome da tabela. O atributo pode ser usado em um `out` parâmetro ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte o [exemplo de C#](#example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Entrada - atributos](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) em parâmetros para gravar valores no armazenamento de tabelas.

Consulte o [exemplo para obter mais detalhes](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `Table`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | n/d | Deve ser definido como `out`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável usada no código da função que representa a tabela ou entidade. Definido como `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**TableName** | O nome da tabela.| 
|**partitionKey** |**PartitionKey** | Chave de partição da entidade de tabela para gravar. Consulte a [seção de uso](#usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de tabela para gravar. Consulte a [seção de uso](#usage) para obter orientação sobre como usar essa propriedade.| 
|**connection** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "mystorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Acesse a entidade da tabela de saída usando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclui as `PartitionKey` `RowKey` Propriedades e. Essas propriedades são geralmente acompanhadas pela implementação `ITableEntity` ou herança `TableEntity` .

Como alternativa, você pode usar um `CloudTable` parâmetro de método para gravar na tabela usando o SDK do armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Acesse a entidade da tabela de saída usando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclui as `PartitionKey` `RowKey` Propriedades e. Essas propriedades são geralmente acompanhadas pela implementação `ITableEntity` ou herança `TableEntity` . O `paramName` valor é especificado na `name` propriedade de *function.jsem*.

Como alternativa, você pode usar um `CloudTable` parâmetro de método para gravar na tabela usando o SDK do armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

Há duas opções para a saída de uma linha de armazenamento de tabela de uma função usando a anotação [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) :

- **Valor de retorno**: ao aplicar a anotação à própria função, o valor de retorno da função é persistido como uma linha de armazenamento de tabela.

- **Imperativo**: para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , onde `T` inclui `PartitionKey` as `RowKey` Propriedades e. Essas propriedades são geralmente acompanhadas pela implementação `ITableEntity` ou herança `TableEntity` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse o evento de saída usando `context.bindings.<name>`, em que `<name>` é o valor especificado na propriedade `name` de *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para gravar em dados de tabela, use o `Push-OutputBinding` cmdlet, defina o `-Name TableBinding` parâmetro e o `-Value` parâmetro igual aos dados da linha. Consulte o [exemplo do PowerShell](#example) para obter mais detalhes.

# <a name="python"></a>[Python](#tab/python)

Há duas opções para a saída de uma mensagem de linha de armazenamento de tabela de uma função:

- **Valor retornado**: defina a propriedade `name` no *function.json* como `$return`. Com essa configuração, o valor de retorno da função é persistido como uma linha de armazenamento de tabela.

- **Imperativo**: passe um valor para [definir](/python/api/azure-functions/azure.functions.out#set-val--t-----none) o método do parâmetro declarado como um tipo de [Saída](/python/api/azure-functions/azure.functions.out). O valor passado para `set` será mantido como uma mensagem do Hub de Eventos.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Tabela | [Códigos de erro de tabelas](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, tabela, fila | [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila | [Solução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
