---
title: Início rápido da biblioteca de clientes da Sugestão Automática do Bing Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 1c3ed6cc97c6fc77083baa915fac2f5530922eac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947200"
---
Introdução à biblioteca de clientes da Sugestão Automática do Bing para Go. Siga estas etapas para instalar a biblioteca e testar nossos exemplos para tarefas básicas.

Use a biblioteca de clientes da Sugestão Automática do Bing para Go para obter sugestões de pesquisa com base em cadeias de caracteres de consulta parciais.

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Código de exemplo](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, [poderá criar uma gratuita](https://azure.microsoft.com/free/cognitive-services).
* A versão mais recente do [Go](https://golang.org/dl/).

Comece a usar a biblioteca de clientes da Sugestão Automática do Bing criando um recurso do Azure. Escolha abaixo o tipo de recurso que é ideal para você:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Criar variáveis de ambiente

>[!NOTE]
> Os pontos de extremidade para recursos criados após 1º de julho de 2019, use o formato de subdomínio personalizado mostrado abaixo. Para saber mais e para obter uma lista completa de pontos de extremidade regionais, confira [Nomes de subdomínio personalizados para Serviços Cognitivos](../../../cognitive-services-custom-subdomains.md).

Usando a chave e o ponto de extremidade do recurso criado, crie duas variáveis de ambiente para autenticação:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: a chave de recurso para autenticar as solicitações.
* `AUTOSUGGEST_ENDPOINT`: o ponto de extremidade do recurso para enviar solicitações de API. O resultado deve ser assim: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Use as instruções para seu sistema operacional.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.

### <a name="macos"></a>[macOS](#tab/unix)

Edite seu `.bash_profile` e adicione a variável de ambiente:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela de console para que as alterações entrem em vigor.
***

## <a name="create-a-new-go-project"></a>Criar um novo projeto do Go

Em uma janela de console (cmd, PowerShell, Terminal, Bash), crie um novo workspace para seu projeto do Go e navegue até ele. Seu workspace conterá três pastas:

* **src**: esse diretório contém o código-fonte e os pacotes. Todos os pacotes instalados com o comando `go get` residirão aqui.
* **pkg**: esse diretório contém os objetos de pacote go compilados. Todos esses arquivos têm uma extensão `.a`.
* **bin**: esse diretório contém os arquivos executáveis binários que são criados quando você executa o `go install`.

> [!TIP]
> Saiba mais sobre a estrutura de um [workspace do Go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para configurar `$GOPATH` e `$GOROOT`.

Vamos criar um workspace chamado `my-app` e os subdiretórios necessários para `src`, `pkg` e `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Instalar a biblioteca de clientes para Go

Agora, vamos instalar a biblioteca de clientes para Go:

```bash
$ go get -u <library-location-or-url>
```

Ou se você usar o DEP, no repositório, execute:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Criar seu aplicativo Go

Em seguida, vamos criar um arquivo chamado `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Abra `sample-app.go`, adicione o nome do pacote e importe as seguintes bibliotecas:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Crie uma função chamada `main`. Em seguida, crie variáveis de ambiente para sua chave e ponto de extremidade da Sugestão Automática do Bing:

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Exemplos de código

Estes exemplos de código mostram como concluir tarefas básicas usando a biblioteca de clientes da Sugestão Automática do Bing para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar uma solicitação de API](#send-an-api-request)

### <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você tenha [criado uma variável de ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) chamada `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` para sua chave da Sugestão Automática do Bing e outra chave chamada `BING_AUTOSUGGEST_ENDPOINT` para seu ponto de extremidade.

Na função `main()`, instancie um cliente com o ponto de extremidade e a chave.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>Enviar uma solicitação de API

No mesmo método, use o método [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) do cliente para enviar uma consulta ao Bing. Em seguida, itere pela resposta [Sugestões](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions) e imprima a primeira sugestão.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo Go com o comando `go run [arguments]` do seu diretório do aplicativo.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Exclua um grupo de recursos no portal do Azure](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Exclua um grupo de recursos na CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial da Sugestão Automática do Bing](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Confira também

- [O que é a Sugestão Automática do Bing?](../../get-suggested-search-terms.md)
- [Referência de API de Sugestão Automática do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
