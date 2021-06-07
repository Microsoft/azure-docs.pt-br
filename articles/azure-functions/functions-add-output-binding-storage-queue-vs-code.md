---
title: Conectar o Azure Functions ao Armazenamento do Azure usando o Visual Studio Code
description: Saiba como conectar o Azure Functions a uma fila de armazenamento do Azure adicionando uma associação de saída ao seu projeto do Visual Studio Code.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 96384d2c50e7d5b4b5b6e652d01c4a89cd519573
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493356"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Conectar o Azure Functions ao Armazenamento do Azure usando o Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra como usar Visual Studio Code para conectar o Armazenamento do Azure à função criada no artigo de início rápido anterior. A associação de saída que você adiciona a essa função escreve dados da solicitação HTTP em uma mensagem na fila. 

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço vinculado. Para facilitar, use a Conta de armazenamento que você criou com o seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="configure-your-local-environment"></a>Configurar o ambiente local

Antes de iniciar este artigo, você deve atender aos seguintes requisitos:

* Instale a [extensão de Armazenamento do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Instale o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/). O Gerenciador de Armazenamento é uma ferramenta que você usará para examinar as mensagens da fila geradas pela associação de saída. O Gerenciador de Armazenamento tem suporte em sistemas operacionais baseados em macOS, Windows e Linux.

::: zone pivot="programming-language-csharp"
* Instale as [ferramentas da CLI do .NET Core](/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

::: zone pivot="programming-language-csharp"  
* Conclua as etapas descritas na [parte 1 do início rápido do Visual Studio Code](create-first-function-vs-code-csharp.md). 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* Conclua as etapas descritas na [parte 1 do início rápido do Visual Studio Code](create-first-function-vs-code-node.md). 
::: zone-end   
::: zone pivot="programming-language-java"  
* Conclua as etapas descritas na [parte 1 do início rápido do Visual Studio Code](create-first-function-vs-code-java.md). 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* Conclua as etapas descritas na [parte 1 do início rápido do Visual Studio Code](create-first-function-vs-code-typescript.md). 
::: zone-end   
::: zone pivot="programming-language-python"  
* Conclua as etapas descritas na [parte 1 do início rápido do Visual Studio Code](create-first-function-vs-code-python.md). 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* Conclua as etapas descritas na [parte 1 do início rápido do Visual Studio Code](create-first-function-vs-code-powershell.md). 
::: zone-end   

Este artigo pressupõe que você já esteja conectado à sua assinatura do Azure do Visual Studio Code. Você pode entrar executando `Azure: Sign In` na paleta de comandos. 

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

No [artigo de início rápido anterior](./create-first-function-vs-code-csharp.md), você criou um aplicativo de funções no Azure, juntamente com a Conta de armazenamento necessária. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você escreverá mensagens em uma Fila de armazenamento na mesma conta. Para se conectar à sua Conta de armazenamento ao executar a função localmente, é necessário baixar as configurações do aplicativo para o arquivo local.settings.json. 

1. Pressione a tecla F1 para abrir a paleta de comandos, pesquise e execute o comando `Azure Functions: Download Remote Settings....`. 

1. Escolha o aplicativo de função que você criou no artigo anterior. Selecione **Sim para todos** para substituir as configurações locais existentes. 

    > [!IMPORTANT]  
    > Como ele contém segredos, o arquivo local.settings.json nunca é publicado e sempre é excluído do controle do código-fonte.

1. Copie o valor `AzureWebJobsStorage`, que é a chave do valor da cadeia de conexão da Conta de armazenamento. Use esta conexão para verificar se a associação de saída funciona conforme o esperado.

## <a name="register-binding-extensions"></a>Registrar as extensões de associação

Como você está usando uma associação de saída do Armazenamento de Filas, você precisa ter a extensão de associações de Armazenamento instalada antes de executar o projeto. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Seu projeto foi configurado para usar [pacotes de extensão](functions-bindings-register.md#extension-bundles), que instalam automaticamente um conjunto predefinido de pacotes de extensão. 

O uso de pacotes de extensão é habilitado no arquivo host.json na raiz do projeto, cuja aparência é semelhante à seguinte:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

Com exceção dos gatilhos de timer e HTTP, as associações são implementadas como pacotes de extensão. Execute o comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) a seguir na janela Terminal para adicionar o pacote de extensão Armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Agora, você pode adicionar a associação de saída do armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

No Functions, cada tipo de associação requer que um `direction`, `type` e um `name` exclusivo seja definido no arquivo functions.json. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

Depois que a associação é definida, você pode usar o `name` da associação para acessá-la como um atributo na assinatura de função. Ao usar uma associação de saída, não é necessário usar o código do SDK do Armazenamento do Azure para se autenticar, para obter uma referência de fila ou para escrever dados. O runtime do Functions e a associação de saída da fila fazem essas tarefas para você.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

## <a name="update-the-tests"></a>Atualizar os testes

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Como no artigo anterior, clique em <kbd>F5</kbd> para iniciar o projeto do aplicativo de funções e o Core Tools. 

1. Com o Core Tools em execução, acesse a área **Azure: Funções**. Em **Funções**, expanda **Projeto Local** > **Funções**. Clique com o botão direito do mouse (CTRL-clique no Mac) na função `HttpExample` e escolha **Executar Função Agora...** .

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Executar a função agora por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`. Clique em ENTER para enviar essa mensagem de solicitação à função.  
 
1. Depois que uma resposta for retornada, clique em <kbd>CTRL + C</kbd> para interromper o Core Tools.

Como você está usando a cadeia de conexão de armazenamento, a função se conecta à conta de armazenamento do Azure durante a execução local. Uma nova fila denominada **outqueue** é criada na sua conta de armazenamento pelo tempo de execução do Functions quando a associação de saída é usada pela primeira vez. Você usará o Gerenciador de Armazenamento para verificar se a fila foi criada junto com a nova mensagem.

### <a name="connect-storage-explorer-to-your-account"></a>Conectar o Gerenciador de Armazenamento à sua conta

Ignore esta seção se você já instalou o Gerenciador de Armazenamento do Azure e o conectou à sua conta do Azure.

1. Execute a ferramenta Gerenciador de Armazenamento do Azure, clique no ícone de conexão à esquerda e selecione **Adicionar uma conta**.

    ![Adicionar uma conta do Azure ao Gerenciador de Armazenamento do Microsoft Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Na caixa de diálogo **Conectar**, escolha **Adicionar uma conta do Azure**, escolha seu **Ambiente do Azure** e selecione **Entrar...** . 

    ![Entre na sua conta do Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Depois de entrar na sua conta, você verá todas as assinaturas do Azure associadas à ela.

### <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No Visual Studio Code, pressione a tecla F1 para abrir a paleta de comandos, em seguida, procure e execute o comando `Azure Storage: Open in Storage Explorer` e escolha o nome da sua conta de armazenamento. Sua conta de armazenamento é aberta no Gerenciador de Armazenamento do Azure.  

1. Expanda o nó **Filas** e selecione a fila denominada **outqueue**. 

   A fila contém a mensagem que a associação de saída de fila criou quando você executou a função disparada por HTTP. Se você tiver invocado a função com o valor `name` padrão do *Azure*, a mensagem da fila será *Nome transmitido à função: Azure*.

    ![Mensagem da fila mostrada no Gerenciador de Armazenamento do Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Execute a função novamente, envie outra solicitação e você verá uma nova mensagem na fila.  

Agora, chegou a hora de republicar o aplicativo de funções atualizado no Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Reimplementar e verificar o aplicativo atualizado

1. No Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione `Azure Functions: Deploy to function app...`.

1. Escolha o aplicativo de funções que você criou no primeiro artigo. Como você está reimplantando seu projeto no mesmo aplicativo, selecione **Implantar** para descartar o aviso de substituição de arquivos.

1. Após a conclusão da implantação, use novamente o recurso **Executar Função Agora...** para disparar a função no Azure.

1. Novamente, [Examinar a Mensagem da fila de armazenamento](#examine-the-output-queue) para verificar se a associação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

No Azure, os *Recursos* se referem a aplicativos de funções, funções, contas de armazenamento e assim por diante. Eles são agrupados em *grupos de recursos* e você pode excluir tudo junto ao excluir o grupo.

Você criou recursos para concluir esses guias de início rápido. Você pode ser cobrado por esses recursos, dependendo do [status de conta](https://azure.microsoft.com/account/) e [preços do serviço](https://azure.microsoft.com/pricing/). Caso não precise mais dos recursos, é possível excluí-los desta maneira:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Próximas etapas

Você atualizou sua função disparada por HTTP para gravar dados em uma Fila de armazenamento. Agora você pode saber mais sobre o desenvolvimento no Functions usando o Visual Studio Code:

+ [Desenvolver no Azure Functions usando o Visual Studio Code](functions-develop-vs-code.md)

+ [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos de função completos em C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completos em JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guia do desenvolvedor de JavaScript do Azure Functions](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Exemplos de projetos de função completos em Java](/samples/browse/?products=azure-functions&languages=java).

+ [Guia do desenvolvedor de Java do Azure Functions](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exemplos de projetos de função completos em TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guia do desenvolvedor de TypeScript do Azure Functions](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exemplos de projetos de função completos no Python](/samples/browse/?products=azure-functions&languages=python).

+ [Guia do desenvolvedor do Python para o Azure Functions](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exemplos de projetos de função completos no PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Guia do desenvolvedor do PowerShell do Azure Functions](functions-reference-powershell.md) 
::: zone-end