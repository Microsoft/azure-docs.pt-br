---
title: Criar uma função Kotlin no Azure Functions usando o Maven
description: Crie e publique um aplicativo de funções disparado por HTTP para o Azure Functions com o Kotlin e o Maven.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 721fbaf1a3b2418677d07e8ac39fa733164c1459
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179552"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Início Rápido: Criar sua primeira função com o Kotlin e o Maven

Este artigo explicará como usar a ferramenta de linha de comando do Maven para criar e publicar um projeto de função Kotlin no Azure Functions. Quando terminar, seu código de função será executado nos [Plano de Consumo](consumption-plan.md) no Azure e poderá ser disparado usando uma solicitação HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções usando Kotlin, você deve ter o seguinte instalado:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior
- [CLI do Azure](/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou posterior

> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

## <a name="generate-a-new-azure-functions-project"></a>Gerar um novo projeto do Azure Functions

Em uma pasta vazia, execute o comando a seguir para gerar o projeto do Azure Functions com base em um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Se você estiver com problemas com a execução do comando, verifique qual versão do `maven-archetype-plugin` é usada. Como você está executando o comando em um diretório vazio sem nenhum arquivo `.pom`, ele pode estar tentando usar um plug-in da versão mais antiga do `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, se você atualizou o Maven de uma versão anterior. Nesse caso, tente excluir o diretório `maven-archetype-plugin` e executar novamente o comando.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

O Maven solicita os valores necessários para concluir a geração do projeto. Para os valores de _groupId_, _artifactId_ e _versão_, consulte a referência [Convenções de nomenclatura Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). O valor de _appName_ deve ser exclusivo no Azure, portanto, o Maven gera um nome de aplicativo com base no _artifactId_ em inserido anteriormente como padrão. O valor de _packageName_ determina o pacote do Kotlin para o código de função gerado.

Os identificadores `com.fabrikam.functions` e `fabrikam-functions` abaixo são usados como um exemplo e tornam as etapas posteriores deste início rápido mais fáceis de ler. Forneça seus valores para o Maven nesta etapa.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

O Maven cria os arquivos de projeto em uma nova pasta com um nome de _artifactId_ neste exemplo `fabrikam-functions`. O código pronto para executar gerado no projeto é uma simples função [disparada por HTTP](./functions-bindings-http-webhook.md) que reflete o corpo da solicitação:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>Executar a função localmente

Altere o diretório para a pasta do projeto recém-criado e compile e execute a função com o Maven:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Caso esteja presenciando esta exceção: `javax.xml.bind.JAXBException`com o Java 9, consulte a solução alternativa no [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Esse resultado é exibido quando a função está sendo executada localmente no seu sistema e está pronta para responder às solicitações HTTP:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Dispare a função na linha de comando usando cURL em uma nova janela de terminal:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

Use `Ctrl-C` no terminal para interromper o código da função.

## <a name="deploy-the-function-to-azure"></a>Implantar a função no Azure

O processo de implantação no Azure Functions usa credenciais de conta da CLI do Azure. [Entre com a CLI do Azure](/cli/azure/authenticate-azure-cli) antes de continuar.

```azurecli
az login
```

Implante seu código em um novo aplicativo de funções usando o destino Maven `azure-functions:deploy`.

> [!NOTE]
> Quando você usa o Visual Studio Code para implantar seu aplicativo de funções, lembre-se de escolher uma assinatura não gratuita ou você receberá um erro. Você pode assistir à sua assinatura no lado esquerdo do IDE.

```
mvn azure-functions:deploy
```

Quando a implantação for concluída, você verá a URL que pode usar para acessar seu aplicativo de funções:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Teste o aplicativo de funções em execução no Azure usando `cURL`. Você precisará alterar a URL do exemplo abaixo para corresponder à URL implantada no seu próprio aplicativo de funções da etapa anterior.

> [!NOTE]
> Verifique se você definiu os **direitos de acesso** para `Anonymous`. Quando você escolhe o nível padrão de `Function`, é necessário que você apresente a [chave da função](functions-bindings-http-webhook-trigger.md#authorization-keys) em solicitações para acessar seu ponto de extremidade da função.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Fazer alterações e implantar novamente

Edite o arquivo de origem `src/main.../Function.java` no projeto gerado para alterar o texto retornado pelo seu aplicativo de funções. Altere esta linha:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

Para o seguinte código:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Salve as alterações e reimplante executando `azure-functions:deploy` do terminal como antes. O aplicativo de funções será atualizado e esta solicitação:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Você verá a saída atualizada:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Associações de referência

Para trabalhar com [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md), exceto o gatilho HTTP e o gatilho de temporizador, você precisa instalar extensões de associação. Embora não seja exigido por este artigo, você precisará saber como habilitar extensões ao trabalhar com outros tipos de associação.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções Kotlin com um gatilho HTTP simples e o implantou no Azure Functions.

- Examine o [Guia do desenvolvedor do Java do Azure Functions](functions-reference-java.md) para saber mais sobre o desenvolvimento de funções Java e Kotlin.
- Adicione outras funções com gatilhos diferentes ao seu projeto usando o destino Maven `azure-functions:add`.
- Escreva e depure funções localmente com [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) e [Eclipse](functions-create-maven-eclipse.md). 
- Depure funções implantadas no Azure com o Visual Studio Code. Veja a documentação de [aplicativos Java sem servidor](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) do Visual Studio Code para obter instruções.
