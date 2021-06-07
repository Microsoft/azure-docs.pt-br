---
title: Criar uma função Java usando o Visual Studio Code – Azure Functions
description: Saiba como criar uma função Java e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions usando a extensão do Azure Functions no Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-java-uiex
ms.openlocfilehash: 10ac677fb65428e8b8855c43b319bbebe7bbbb71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701425"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Início rápido: Criar uma função Java no Azure usando o Visual Studio Code

> [!div class="op_single_selector" title1="Selecione a linguagem da função: "]
> - [Java](create-first-function-vs-code-java.md)
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Outro (Go/Rust)](create-first-function-vs-code-other.md)

Neste artigo, você usará o Visual Studio Code para criar uma função do Java que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

> [!NOTE]
> Se o Visual Studio Code não for a ferramenta de desenvolvimento que você prefere, confira nossos tutoriais semelhantes para desenvolvedores Java usando [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) e [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="configure-your-environment"></a>Configurar seu ambiente

Antes de começar, verifique se você tem os seguintes requisitos implementados:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ O [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versão 8 ou 11.

+ [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.

+ [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ O [pacote de extensão do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ A [Extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Criar seu projeto local

Nesta seção, você usará o Visual Studio Code para criar um projeto local do Azure Functions em Java. Mais adiante neste artigo, você publicará o código de função no Azure. 

1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions** e selecione o ícone **Criar projeto...** .

    ![Escolher Criar um projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o workspace do projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estas etapas foram projetadas para serem concluídas fora de um workspace. Nesse caso, não selecione uma pasta de projeto que faz parte de um workspace.

1. Forneça as seguintes informações nos prompts:

    + **Selecione uma linguagem de programação para o seu projeto de função**: Escolha `Java`.

    + **Selecione uma versão do Java**: escolha `Java 8` ou `Java 11`, a versão do Java em que suas funções são executadas no Azure. Escolha uma versão do Java verificada localmente.

    + **Forneça uma ID de grupo**: Escolha `com.function`.

    + **Forneça uma ID de artefato**: Escolha `myFunction`.

    + **Forneça uma versão**: Escolha `1.0-SNAPSHOT`.

    + **Forneça um nome de pacote**: Escolha `com.function`.

    + **Forneça um nome de aplicativo**: Escolha `myFunction-12345`.

    + **Nível de autorização**: Escolha `Anonymous`, que permite que qualquer pessoa chame seu ponto de extremidade de função. Para saber mais sobre o nível de autorização, confira [Chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como você gostaria de abrir seu projeto**: Escolha `Add to workspace`.

1. Usando essas informações, o Visual Studio Code gera um projeto do Azure Functions com um gatilho HTTP. Você pode exibir os arquivos de projeto locais no Explorer. Para saber mais sobre os arquivos criados, confira [Arquivos de projeto gerados](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Após verificar se a função foi executada corretamente no computador local, é hora de usar o Visual Studio Code para publicar o projeto diretamente no Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Próximas etapas

Você usou o [Visual Studio Code](functions-develop-vs-code.md?tabs=java) para criar um aplicativo de funções com uma função simples disparada por HTTP. No próximo artigo, você expandirá essa função conectando-se ao Armazenamento do Azure. Para saber mais sobre como se conectar a outros serviços do Azure, confira [Adicionar associações a uma função existente no Azure Functions](add-bindings-existing-function.md?tabs=java). 

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
