---
title: Conectar a função Java ao Armazenamento do Azure
description: Saiba como conectar uma função Java disparada por HTTP ao Armazenamento do Azure usando uma associação de saída do Armazenamento de Filas.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422647"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Conectar a função Java ao Armazenamento do Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra como integrar a função criada no [artigo de início rápido anterior](./create-first-function-cli-java.md?tabs=bash,browser) com uma fila de Armazenamento do Azure. A associação de saída que você adiciona a essa função escreve dados de uma solicitação HTTP em uma mensagem na fila.

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço vinculado. Para facilitar essa conexão, use a conta de armazenamento que você criou com o seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, conclua as etapas na [parte 1 do início rápido do Java](./create-first-function-cli-java.md?tabs=bash,browser).

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora, você pode adicionar a associação de saída do Armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Agora você está pronto para experimentar a nova associação de saída localmente.

## <a name="run-the-function-locally"></a>Executar a função localmente

Como anteriormente, use o seguinte comando para compilar o projeto e iniciar o Functions Runtime localmente:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Como você habilitou os pacotes de extensões no host.json, a [extensão de associação do armazenamento](functions-bindings-storage-blob.md#add-to-your-functions-app) foi baixada e instalada para você durante a inicialização, juntamente com outras extensões de associação da Microsoft.

Como anteriormente, dispare a função na linha de comando usando o cURL em uma nova janela de terminal:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Dessa vez, a associação de saída também cria uma fila denominada `outqueue` em sua Conta de armazenamento e adiciona uma mensagem com essa mesma cadeia de caracteres.

Em seguida, use a CLI do Azure para exibir a nova fila e verifique se uma mensagem foi adicionada. Também é possível exibir sua fila usando o [Gerenciador de Armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Reimplantar o projeto 

Para atualizar o aplicativo publicado, execute o seguinte comando novamente:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Novamente, use o cURL para testar a função implantada. Como anteriormente, passe o valor `AzureFunctions` no corpo da solicitação POST para a URL, como neste exemplo:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

[Examine a mensagem da Fila de armazenamento](#query-the-storage-queue) novamente para verificar se a associação de saída gera uma nova mensagem na fila, conforme esperado.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Você atualizou a função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre como desenvolver o Azure Functions com o Java, confira o [Guia do desenvolvedor de Java para o Azure Functions](functions-reference-java.md) e [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md). Para obter exemplos de projetos completos de funções em Java, confira as [Amostras de funções Java](/samples/browse/?products=azure-functions&languages=Java). 

Em seguida, você deve habilitar o monitoramento do Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Habilitar a integração do Application Insights](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
