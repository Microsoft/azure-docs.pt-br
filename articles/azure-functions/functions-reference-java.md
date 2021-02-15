---
title: Referência do desenvolvedor de Java para o Azure Functions
description: Entenda como desenvolver funções usando Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1ffbd760ae75605d75652b29d379420d6946aa8f
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326447"
---
# <a name="azure-functions-java-developer-guide"></a>Guia do desenvolvedor de Java do Azure Functions

Este guia contém informações detalhadas para ajudá-lo a desenvolver com sucesso Azure Functions usando o Java.

Como desenvolvedor de Java, se você for novo no Azure Functions, considere primeiro ler um dos seguintes artigos:

| Introdução | Conceitos| 
| -- | -- |  
| <ul><li>[Função Java usando Visual Studio Code](./create-first-function-vs-code-java.md)</li><li>[Função Java/Maven com terminal/prompt de comando](./create-first-function-cli-java.md)</li><li>[Função Java usando gradle](functions-create-first-java-gradle.md)</li><li>[Função Java usando o eclipse](functions-create-maven-eclipse.md)</li><li>[Função Java usando a ideia IntelliJ](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Guia do desenvolvedor](functions-reference.md)</li><li>[Opções de hospedagem](functions-scale.md)</li><li>[&nbsp;Considerações sobre desempenho](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Noções básicas da função Java

Uma função Java é um método `public`, decorado com a anotação `@FunctionName`. Esse método define a entrada para uma função Java e deve ser exclusivo em um pacote específico. O pacote pode ter várias classes com vários métodos públicos anotados com `@FunctionName` . Um único pacote é implantado em um aplicativo de funções no Azure. Ao executar no Azure, o aplicativo de funções fornece a implantação, execução e contexto de gerenciamento para suas funções Java individuais.

## <a name="programming-model"></a>Modelo de programação 

Os conceitos de [gatilhos e ligações](functions-triggers-bindings.md) são fundamentais para as Funções do Azure. Os gatilhos iniciam a execução do seu código. As ligações fornecem uma maneira de transmitir dados e retornar dados de uma função, sem precisar escrever código de acesso a dados personalizado.

## <a name="create-java-functions"></a>Criar funções Java

Para facilitar a criação de funções Java, há ferramentas baseadas no Maven e arquétipos que usam modelos Java predefinidos para ajudá-lo a criar projetos com um gatilho de função específico.    

### <a name="maven-based-tooling"></a>Ferramentas baseadas no Maven

Os seguintes ambientes de desenvolvedor têm ferramentas do Azure Functions que permitem criar projetos de funções Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Os links do artigo acima mostram como criar suas primeiras funções usando o IDE de sua escolha. 

### <a name="project-scaffolding"></a>Scaffolding do projeto

Caso você prefira o desenvolvimento de linha de comando do Terminal, a maneira mais simples de fazer o scaffolding de projetos de funções baseados em Java é com arquétipos `Apache Maven`. O arquétipo Java Maven para o Azure Functions é publicado na seguinte _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

O seguinte comando gera um novo projeto de função Java usando esse arquétipo:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

Para começar a usar esse arquétipo, confira o [Início rápido do Java](./create-first-function-cli-java.md).

## <a name="folder-structure"></a>Estrutura de pastas

Aqui está a estrutura de pastas de um projeto Java do Azure Functions:

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

Você pode usar um arquivo [host.json](functions-host-json.md) compartilhado para configurar o aplicativo de funções. Cada função possui seu próprio arquivo de código (.java) e arquivo de configuração de associação (function.json).

Você pode colocar mais de uma função em um projeto. Evite colocar suas funções no jars separados. O `FunctionApp` no diretório de destino é o que é implantado em seu aplicativo de funções no Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções são invocadas por um gatilho, como uma solicitação HTTP, um temporizador ou uma atualização para os dados. Sua função precisa processar esse gatilho e quaisquer outras entradas para produzir uma ou mais saídas.

Use as anotações Java incluídas no pacote [com.microsoft.azure.functions.annotation. *](/java/api/com.microsoft.azure.functions.annotation) para ligar entradas e saídas a seus métodos. Para obter mais informações, confira os [documentos de referência Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Você precisa configurar uma conta de Armazenamento do Azure em [local.settings.json](./functions-run-local.md#local-settings-file) para executar localmente os gatilhos de armazenamento de Blob do Azure, armazenamento de Filas do Azure ou Armazenamento de Tabelas do Azure.

Exemplo:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Aqui está o `function.json` correspondente gerado pelo [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="java-versions"></a>Versões do Java

A versão do Java usada durante a criação do aplicativo de funções no qual as funções são executadas no Azure é especificada no arquivo de pom.xml. O arquétipo Maven atualmente gera um pom.xml para Java 8, que você pode alterar antes da publicação. A versão do Java no pom.xml deve corresponder à versão na qual você desenvolveu e testou localmente seu aplicativo. 

### <a name="supported-versions"></a>Versões com suporte

A tabela a seguir mostra as versões Java com suporte atuais para cada versão principal do tempo de execução do functions, por sistema operacional:

| Versão do Functions | Versões Java (Windows) | Versões Java (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2. x | 8 | N/D |

A menos que você especifique uma versão Java para a sua implantação, o padrão Maven o Java 8 durante a implantação no Azure.

### <a name="specify-the-deployment-version"></a>Especificar a versão de implantação

Você pode controlar a versão do Java direcionada pelo arquétipo Maven usando o `-DjavaVersion` parâmetro. O valor desse parâmetro pode ser `8` ou `11` . 

O arquétipo do Maven gera um pom.xml que tem como destino a versão do Java especificada. Os seguintes elementos no pom.xml indicam a versão do Java a ser usada:

| Elemento |  Valor de Java 8 | Valor do Java 11 | Descrição |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Versão do Java usada pelo plug-in Maven-Compiler-. |
| **`JavaVersion`** | 8 | 11 | Versão do Java hospedada pelo aplicativo de funções no Azure. |

Os exemplos a seguir mostram as configurações para Java 8 nas seções relevantes do arquivo pom.xml:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> Você deve ter a variável de ambiente JAVA_HOME definida corretamente para o diretório do JDK que é usado durante a compilação de código usando o Maven. Certifique-se de que a versão do JDK seja pelo menos tão alta quanto a `Java.version` configuração. 

### <a name="specify-the-deployment-os"></a>Especificar o so de implantação

O Maven também permite especificar o sistema operacional no qual seu aplicativo de funções é executado no Azure. Use o `os` elemento para escolher o sistema operacional. 

| Elemento |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | Linux | docker |

O exemplo a seguir mostra a configuração do sistema operacional na `runtime` seção do arquivo pom.xml:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>Suporte e disponibilidade de runtime do JDK 

Para o desenvolvimento local de aplicativos de funções Java, baixe e use o [azul Zulu Enterprise para o Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDKs dos [sistemas azul](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions usa um tempo de execução de Java JDK azul quando você implanta seu aplicativo de funções na nuvem.

O [Suporte do Azure](https://azure.microsoft.com/support/) está disponível para solucionar problemas com os JDKs e os aplicativos de funções com um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Personalizar JVM

O Functions permite que você personalize a JVM (máquina virtual Java) usada para executar suas funções Java. As [seguintes opções de JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) são usadas por padrão:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Você pode fornecer argumentos adicionais em uma configuração de aplicativo chamada `JAVA_OPTS`. Você pode adicionar configurações ao seu aplicativo de funções implantado no Azure no portal do Azure ou na CLI do Azure.

> [!IMPORTANT]  
> No plano de consumo, você também deve adicionar a configuração de WEBSITE_USE_PLACEHOLDER com um valor 0 para que a personalização funcione. Essa configuração aumenta as horas de inicialização a frio para funções Java.

### <a name="azure-portal"></a>Portal do Azure

No [portal do Azure](https://portal.azure.com), use a guia [Configurações de Aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) para adicionar a configuração `JAVA_OPTS`.

### <a name="azure-cli"></a>CLI do Azure

Você pode usar o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) para definir `JAVA_OPTS`, como no seguinte exemplo:

# <a name="consumption-plan"></a>[Plano de consumo](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[Plano de consumo](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Plano dedicado/plano Premium](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Plano dedicado/plano Premium](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

Este exemplo habilita o modo sem periféricos. Substitua `<APP_NAME>` pelo nome do seu aplicativo de funções e `<RESOURCE_GROUP>` pelo grupo de recursos. 

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

O Azure Functions oferece suporte ao uso de bibliotecas de terceiros. Por padrão, todas as dependências especificadas no arquivo `pom.xml` do projeto são agrupadas automaticamente durante a meta [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage). Para bibliotecas não especificadas como dependências no `pom.xml`arquivo, coloque-as em um diretório`lib` no diretório-raiz da função. As dependências colocadas no diretório `lib` são adicionadas ao carregador de classe do sistema em runtime.

A dependência `com.microsoft.azure.functions:azure-functions-java-library` é fornecida no classpath por padrão e não precisa ser incluída no diretório `lib`. Além disso,o [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) adiciona as dependências listadas [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) ao classpath.

## <a name="data-type-support"></a>Suporte do tipo de dados

Você pode usar POJOs (objetos Java básicos), tipos definidos em `azure-functions-java-library` ou tipos de dados primitivos, como String e Integer, para vincular a associações de entrada ou saída.

### <a name="pojos"></a>POJOs

Para converter dados de entrada em POJO, o [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) usa a biblioteca [gson](https://github.com/google/gson). Tipos de POJO usados como entradas para funções devem ser `public`.

### <a name="binary-data"></a>Dados binários

Associe entradas ou saídas binárias a `byte[]`, definindo o campo `dataType` em function.json como `binary`:

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

Se você espera valores nulos, use `Optional<T>`.

## <a name="bindings"></a>Associações

As ligações de entrada e saída fornecem uma maneira declarativa de se conectar aos dados de dentro do seu código. Uma função pode ter várias ligações de entrada e saída.

### <a name="input-binding-example"></a>Exemplo de associação de entrada

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
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

Você invoca essa função com uma solicitação HTTP. 
- A carga de solicitação HTTP é passada como um `String` para o argumento `inputReq`.
- Uma entrada é recuperada do armazenamento de tabela e é passada como `TestInputData` para o argumento `inputData`.

Para receber um lote de entradas, você pode associar a `String[]`, `POJO[]`, `List<String>` ou `List<POJO>`.

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

Essa função é acionada sempre que houver novos dados no hub de eventos configurado. Como q `cardinality` é definidq para `MANY`, a função recebe um lote de mensagens do hub de eventos. `EventData` do hub de eventos é convertido em `TestEventData` para a execução da função.

### <a name="output-binding-example"></a>Exemplo de associação de saída

Você pode vincular uma associação de saída ao valor retornado usando `$return`. 

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

Você invoca essa função em uma HttpRequest. Ela grava vários valores no armazenamento de Filas.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Eles são definidos em `azure-functions-java-library`. Eles são tipos auxiliares para trabalhar com as funções HttpTrigger.

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Gatilho de HTTP     | Obtém método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Associação de saída HTTP | Retorna um status diferente de 200   |

## <a name="metadata"></a>Metadados

Poucos gatilhos enviam [gatilhos de metadados](./functions-triggers-bindings.md) juntamente com dados de entrada. Você pode usar a anotação `@BindingName` para associar aos metadados do gatilho.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
No exemplo anterior, o `queryValue` está associado ao parâmetro de cadeia de caracteres de consulta `name` na URL de solicitação HTTP `http://{example.host}/api/metadata?name=test`. Aqui está outro exemplo, mostrando como associar a `Id` de metadados de gatilho de fila.

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
> O nome fornecido na anotação precisa corresponder à propriedade de metadados.

## <a name="execution-context"></a>Contexto de execução

`ExecutionContext` definido na `azure-functions-java-library` contém métodos auxiliares para se comunicar com o runtime das funções. Para obter mais informações, consulte o [artigo de referência do ExecutionContext](/java/api/com.microsoft.azure.functions.executioncontext).

### <a name="logger"></a>Agente

Use `getLogger`, definido em `ExecutionContext`, para gravar logs do código de função.

Exemplo:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Exibir logs e rastreamento

Você pode usar a CLI do Azure para transmitir o log Java stdout e stderr, bem como outros logs de aplicativo. 

Confira como configurar seu aplicativo de funções para gravar o log do aplicativo usando a CLI do Azure:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

Para transmitir a saída de log de seu aplicativo de funções usando a CLI do Azure, abra um novo prompt de comando, Bash ou sessão de terminal e digite o seguinte comando:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

O comando [az webapp log tail](/cli/azure/webapp/log) tem opções para filtrar a saída usando a opção `--provider`. 

Para baixar os arquivos de log como um único arquivo ZIP usando a CLI do Azure, abra um novo prompt de comando, Bash ou sessão de terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Você deve ter habilitado o log do sistema de arquivos no Portal do Azure ou na CLI do Azure antes de executar esse comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](functions-app-settings.md), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando `System.getenv("AzureWebJobsStorage")`.

O exemplo a seguir obtém a [configuração de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings), com a chave chamada `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> O valor de AppSetting FUNCTIONS_EXTENSION_VERSION deve ser ~ 2 ou ~ 3 para uma experiência de inicialização a frio otimizada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o desenvolvimento de Java do Azure Functions confira os seguintes recursos:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* Desenvolvimento local e depuração com o [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) e [Eclipse](functions-create-maven-eclipse.md)
* [Funções Java de Depuração Remota usando o Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in do Maven para Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Simplifique a criação de funções por meio da meta `azure-functions:add` e prepare um diretório de preparo para a [implantação do arquivo ZIP](deployment-zip-push.md).
