---
title: Criar sua primeira função no Azure com Java e Maven | Microsoft Docs
description: Criar e publicar uma função simples disparada por HTTP no Azure com Java e Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 20327e64080182518fd28b1d367ffe37be5ce9a4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323958"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Criar sua primeira função com Java e Maven

Este artigo orienta você por meio pelo uso da ferramenta de linha de comando Maven para criar e publicar uma função Java para o Azure Functions. Quando terminar, seu código de função será executado nos [Plano de Consumo](functions-scale.md#consumption-plan) no Azure e poderá ser disparado usando uma solicitação HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções usando Java, você deve ter o seguinte instalado:

- [Java Developer Kit](https://aka.ms/azure-jdks), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior
- [CLI do Azure](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou posterior

> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

## <a name="generate-a-new-functions-project"></a>Gerar um novo projeto do Functions

Em uma pasta vazia, execute o seguinte comando para gerar o projeto do Functions a partir de um [arquétipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Se você estiver com problemas com a execução do comando, verifique qual versão do `maven-archetype-plugin` é usada. Como você está executando o comando em um diretório vazio sem nenhum arquivo `.pom`, ele pode estar tentando usar um plug-in da versão mais antiga do `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, se você atualizou o Maven de uma versão anterior. Nesse caso, tente excluir o diretório `maven-archetype-plugin` e executar novamente o comando.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

O Maven solicitará os valores necessários para concluir a geração do projeto. Para os valores de _groupId_, _artifactId_ e _versão_, consulte a referência [Convenções de nomenclatura Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). O valor de _appName_ deve ser exclusivo no Azure, portanto, o Maven gera um nome de aplicativo com base no _artifactId_ em inserido anteriormente como padrão. O valor de _packageName_ determina o pacote de Java para o código de função gerado.

Os identificadores `com.fabrikam.functions` e `fabrikam-functions` abaixo são usados como um exemplo e tornam as etapas posteriores deste início rápido mais fáceis de ler. Forneça seus próprios valores para o Maven nesta etapa.

```Output
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

O Maven cria os arquivos de projeto em uma nova pasta com um nome de _artifactId_ neste exemplo `fabrikam-functions`. O código pronto para executar gerado no projeto é uma função [disparada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) que reflete o corpo da solicitação. Substitua *src/main/java/com/fabrikam/functions/Function.java* pelo seguinte código: 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Executar a função localmente

Altere o diretório para a pasta do projeto recém-criado e compile e execute a função com o Maven:

```CMD
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Caso esteja presenciando esta exceção: `javax.xml.bind.JAXBException`com o Java 9, consulte a solução alternativa no [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Esse resultado é exibido quando a função está sendo executada localmente no seu sistema e está pronta para responder às solicitações HTTP:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Dispare a função na linha de comando usando cURL em uma nova janela de terminal:

```CMD
curl -w "\n" http://localhost:7071/api/hello -d LocalFunction
```

```Output
Hello LocalFunction!
```

Use `Ctrl-C` no terminal para interromper o código da função.

## <a name="deploy-the-function-to-azure"></a>Implantar a função no Azure

O processo de implantação no Azure Functions usa credenciais de conta da CLI do Azure. [Entre com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```

Implante seu código em um novo aplicativo de funções usando o destino Maven `azure-functions:deploy`. Isso executa uma [Implantação de Zip com o modo de execução do pacote](functions-deployment-technologies.md#zip-deploy) habilitado.

> [!NOTE]
> Quando você usa o Visual Studio Code para implantar seu aplicativo Function, lembre-se de escolher uma assinatura não gratuita, ou você receberá um erro. Você pode assistir à sua assinatura no lado esquerdo do IDE.

```azurecli
mvn azure-functions:deploy
```

Quando a implantação for concluída, você verá a URL que pode usar para acessar seu aplicativo de funções do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Teste o aplicativo de funções em execução no Azure usando `cURL`. Você precisará alterar a URL do exemplo abaixo para corresponder à URL implantada no seu próprio aplicativo de funções da etapa anterior.

> [!NOTE]
> Verifique se você definiu os **direitos de acesso** para `Anonymous`. Quando você escolhe o nível padrão de `Function`, é necessário que você apresente a [chave da função](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) em solicitações para acessar seu ponto de extremidade da função.

```azurecli
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Fazer alterações e implantar novamente

Edite o arquivo de origem `src/main.../Function.java` no projeto gerado para alterar o texto retornado pelo seu aplicativo de Funções. Altere esta linha:

```java
return request.createResponse(200, "Hello, " + name);
```

Para o seguinte:

```java
return request.createResponse(200, "Hi, " + name);
```

Salve as alterações. Execute mvn clean package e reimplante executando `azure-functions:deploy` no terminal como antes. O aplicativo de funções será atualizado e esta solicitação:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/hello
```

Terá a saída atualizada:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções Java com um gatilho HTTP simples e o implantou no Azure Functions.

- Examine o [Guia do desenvolvedor de funções Java](functions-reference-java.md) para saber mais sobre o desenvolvimento de funções Java.
- Adicione outras funções com gatilhos diferentes ao seu projeto usando o destino Maven `azure-functions:add`.
- Escreva e depure funções localmente com [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) e [Eclipse](functions-create-maven-eclipse.md). 
- Depure funções implantadas no Azure com o Visual Studio Code. Veja a documentação de [aplicativos Java sem servidor](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) do Visual Studio Code para obter instruções.
