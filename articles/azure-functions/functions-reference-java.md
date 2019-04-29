---
title: Referência do desenvolvedor de Java do Azure Functions | Microsoft Docs
description: Entenda como desenvolver funções usando Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 9258b58783d4670620a251fef866211f7634480f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020360"
---
# <a name="azure-functions-java-developer-guide"></a>Guia do desenvolvedor de Java do Azure Functions

O tempo de execução do Azure Functions dá suporte a [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/).

Este guia contém informações sobre a complexidade da gravação de funções do Azure com Java.

Uma função de Java é um `public` método decorado com anotação `@FunctionName`. Esse método define a entrada para uma função de java e deve ser exclusivo em um determinado pacote. 

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Você também deve concluir o guia de início rápido funções para criar sua primeira função, usando [Visual Studio Code](functions-create-first-function-vs-code.md) ou [usando o maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Modelo de programação 

Os conceitos de [gatilhos e ligações](functions-triggers-bindings.md) são fundamentais para as Funções do Azure. Os gatilhos iniciam a execução do seu código. As ligações fornecem uma maneira de transmitir dados e retornar dados de uma função, sem precisar escrever código de acesso a dados personalizado.

## <a name="folder-structure"></a>Estrutura de pastas

Aqui está a estrutura de pastas de um projeto Java da função do Azure:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função possui seu próprio arquivo de código (.java) e arquivo de configuração de associação (function.json).

Você pode colocar mais de uma função em um projeto. Evite colocar suas funções no jars separados. O FunctionApp no diretório de destino é o que é implantado para seu aplicativo de funções no Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções do Azure são chamadas por um disparador, como uma solicitação HTTP, um cronômetro ou uma atualização de dados. A função precisa processar esse gatilho e quaisquer outras entradas para produzir uma ou mais saídas.

Use as anotações Java incluídas no pacote [com.microsoft.azure.functions.annotation. *](/java/api/com.microsoft.azure.functions.annotation) para ligar entradas e saídas a seus métodos. Para mais informações, consulte [documentação de referência Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Você precisa configurar uma conta de Armazenamento do Azure em seu [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) para executar gatilhos de Tabela, Fila ou Azure Storage Blob localmente.

Exemplo:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

aqui está o correspondente gerado `function.json` pelo [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
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

## <a name="jdk-runtime-availability-and-support"></a>Suporte e disponibilidade de tempo de execução do JDK 

Baixe e use os JDKs Java 8 do [Azul Zulu Enterprise para Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) da [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) para desenvolvimento local de aplicativos de funções Java. O Azure Functions usa o tempo de execução do JDK Java 8 da Azul quando você implanta seus aplicativos de funções na nuvem.

O [Suporte do Azure](https://azure.microsoft.com/en-us/support/) está disponível para solucionar problemas com os JDKs e os aplicativos de funções com um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

O Azure Functions oferece suporte ao uso de bibliotecas de terceiros. Por padrão, todas as dependências especificadas no arquivo `pom.xml` do projeto serão agrupadas automaticamente durante a meta [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage). Para bibliotecas não especificadas como dependências no `pom.xml`arquivo, coloque-as em um diretório`lib` no diretório-raiz da função. Dependências colocadas o `lib` diretório será adicionado ao carregador de classe do sistema em tempo de execução.

A `com.microsoft.azure.functions:azure-functions-java-library` dependência é fornecida no caminho de classe por padrão e não precisa ser incluído no `lib` diretório. Além disso, as dependências listadas [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) são adicionadas ao caminho de classe por [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Suporte ao tipo de dados

É possível usar POJOs (objetos Java antigos) simples, tipos definidos em `azure-functions-java-library` ou dataTypes primitivos, como String, Integer para vincular a ligações de entrada / saída.

### <a name="plain-old-java-objects-pojos"></a>POJOs (objetos Java básicos)

Para converter dados de entrada para POJO, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) usa a biblioteca [gson](https://github.com/google/gson). Tipos de POJO usados como entradas para funções devem ser `public`.

### <a name="binary-data"></a>Dados binários

Ligue entradas ou saídas binárias a `byte[]`configurando o campo`dataType` na sua função.json para `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Use `Optional<T>` se valores nulos são esperados

## <a name="bindings"></a>Associações

As ligações de entrada e saída fornecem uma maneira declarativa de se conectar aos dados de dentro do seu código. Uma função pode ter várias ligações de entrada e saída.

### <a name="example-input-binding"></a>Exemplo de associação de entrada

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Essa função é chamada com uma solicitação HTTP. 
- A carga útil do pedido HTTP é passada como `String`para o argumento`inputReq`
- Uma entrada é recuperada do Armazenamento de Tabela do Azure e é transmitida como `TestInputData` para o argumento `inputData`.

Para receber um lote de entradas, você pode ligar para `String[]`, `POJO[]`, `List<String>` ou `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Essa função é acionada sempre que houver novos dados no hub de eventos configurado. Como o `cardinality` está definido como `MANY`, a função recebe um lote de mensagens do hub de eventos. EventData do hub de eventos serão convertidos em `TestEventData` para a execução da função.

### <a name="example-output-binding"></a>Exemplo de ligação de saída

Você pode ligar uma ligação de saída ao valor de retorno usando `$return` 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Se houver várias associações de saída, use o valor de retorno de apenas um deles.

Para enviar vários valores de saída, use `OutputBinding<T>` definido no pacote `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

A função acima é invocada em um HttpRequest e grava vários valores na fila do Azure

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Os tipos HttpRequestMessage e HttpResponseMessage estão definidos em `azure-functions-java-library` são tipos auxiliares para trabalhar com funções HttpTrigger

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Gatilho de HTTP     | Obter método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Associação de saída HTTP | Retornar status diferentes de 200   |

## <a name="metadata"></a>Metadados

Poucos gatilhos enviam [gatilhos de metadados](/azure/azure-functions/functions-triggers-bindings) juntamente com dados de entrada. Você pode usar a anotação `@BindingName` para ligar aos metadados do acionador


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
No exemplo acima, o `queryValue` está vinculado ao parâmetro de string de consulta `name` na URL de solicitação HTTP `http://{example.host}/api/metadata?name=test`. A seguir, outro exemplo para vincular-se `Id` a partir de metadados do acionador de fila

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> O nome fornecido na anotação precisa corresponder à propriedade de metadados

## <a name="execution-context"></a>Contexto de execução

`ExecutionContext` definido no `azure-functions-java-library` contém métodos auxiliares para se comunicar com o tempo de execução das funções.

### <a name="logger"></a>Agente

Use `getLogger` definido em `ExecutionContext` para gravar logs do código de função.

Exemplo:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Exibir logs e rastreamento

Você pode usar a CLI do Azure para transmitir logs de stdout e stderr Java, bem como outros logs de aplicativos. 

Configure seu aplicativo de função para gravar o log do aplicativo usando a CLI do Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir a saída de log para seu aplicativo de Função usando a CLI do Azure, abra um novo prompt de comando, Bash ou sessão de Terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O [final de log do az webapp](/cli/azure/webapp/log) comando tem opções para filtrar a saída usando o `--provider` opção. 

Para baixar os arquivos de log como um único arquivo ZIP usando a CLI do Azure, abra um novo prompt de comando, Bash ou sessão de Terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Você deve ter habilitado o log do sistema de arquivos no Portal do Azure ou na CLI do Azure antes de executar este comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando, `System.getenv("AzureWebJobsStorage")`

Exemplo:

Adicione [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) com o nome testAppSetting e valor testAppSettingValue

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o desenvolvimento de Java de função do Azure, consulte os seguintes recursos:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* Local de desenvolvimento e depuração com o [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), e [Eclipse](functions-create-maven-eclipse.md). 
* [Azure Functions de Java de Depuração Remota com Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plugin Maven para as Funções do Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) - simplifique a criação de funções por meio da meta `azure-functions:add` e prepare um diretório temporário para a [implantação do arquivo ZIP](deployment-zip-push.md).
