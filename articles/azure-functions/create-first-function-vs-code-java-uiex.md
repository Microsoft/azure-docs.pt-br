---
title: Criar uma função Java usando o Visual Studio Code – Azure Functions
description: Saiba como criar uma função Java e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions usando a extensão do Azure Functions no Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c84bf4ebc73857fa0280ffbcbf46b68c2da630f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031711"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Início rápido: Criar uma função Java no Azure usando o Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Use o Visual Studio Code para criar uma função Java que responda a solicitações HTTP. Teste o código localmente e implante-o no ambiente sem servidor do Azure Functions.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">Conta do Azure</abbr>.

Se o Visual Studio Code não for a ferramenta de desenvolvimento que você prefere, confira nossos tutoriais semelhantes para desenvolvedores Java usando [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) e [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

Antes de começar, verifique se você tem os seguintes requisitos implementados:

+ Uma conta do Azure com uma assinatura ativa <abbr title="A estrutura organizacional básica na qual você gerencia recursos no Azure, normalmente associada a um indivíduo ou departamento da organização.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ O [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versão 8 ou 11.

+ [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.

+ [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ O [pacote de extensão do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ A [Extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a>Criar o projeto local do Functions

1. Escolha o ícone do Azure na **Barra de atividade** e, na área **Azure: Functions**, selecione o ícone **Criar projeto...** .

    ![Escolher Criar um projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Escolha um local de diretório** para o workspace do projeto e escolha **Selecionar**.

1. Forneça as seguintes informações nos prompts:

    + **Selecione uma linguagem de programação para o seu projeto de função**: Escolha `Java`.

    + **Selecione uma versão do Java**: escolha `Java 8` ou `Java 11`, a versão do Java em que suas funções são executadas no Azure. Escolha uma versão do Java verificada localmente.

    + **Forneça uma ID de grupo**: Escolha `com.function`.

    + **Forneça uma ID de artefato**: Escolha `myFunction`.

    + **Forneça uma versão**: Escolha `1.0-SNAPSHOT`.

    + **Forneça um nome de pacote**: Escolha `com.function`.

    + **Forneça um nome de aplicativo**: Escolha `myFunction-12345`.

    + **Nível de autorização**: Escolha `Anonymous`, que permite que qualquer pessoa chame seu ponto de extremidade de função.

    + **Selecione como você gostaria de abrir seu projeto**: Escolha `Add to workspace`.

<br/>

<details>
<summary><strong>Não consegue criar um projeto de função?</strong></summary>

Os problemas mais comuns a serem resolvidos ao criar um projeto local no Functions são:
* Você não tem a extensão do Azure Functions instalada. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Executar a função localmente

1. Pressione <kbd>F5</kbd> para iniciar o projeto do aplicativo de funções.

1. No **Terminal**, veja o ponto de extremidade da URL de sua função em execução no local.

    ![Saída do VS Code de função local](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Com o Core Tools em execução, acesse a área **Azure: Funções**. Em **Funções**, expanda **Projeto Local** > **Funções**. Clique com o botão direito do mouse (Windows) ou clique em <kbd>CTRL -</kbd> (macOS) na função `HttpExample` e escolha **Função Executar Agora...** .

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Executar a função agora por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`. Pressione <kbd>Enter</kbd> para enviar essa mensagem de solicitação à função.  

1. Quando a função é executada localmente e retorna uma resposta, uma notificação é gerada no Visual Studio Code. As informações sobre a execução da função são mostradas no painel **Terminal**.

1. Pressione <kbd>Ctrl + C</kbd> para parar o Core Tools e desconectar o depurador.

<br/>

<details>
<summary><strong>Não consegue executar a função localmente?</strong></summary>

Os problemas mais comuns a serem resolvidos ao executar um projeto local no Functions são:
* Você não tem as Core Tools instaladas. 
*  Se você tiver problemas com a execução no Windows, verifique se o shell do terminal padrão do Visual Studio Code não está definido como Bash WSL. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Entrar no Azure

Para publicar o aplicativo, entre no Azure. Se você já tiver entrado, vá para a próxima seção.

1. Escolha o ícone do Azure na barra de Atividade e, na área **Azure: Functions**, escolha **Entrar no Azure...** .

    ![Entrar no Azure por meio do VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Quando solicitado no navegador, **escolha sua conta do Azure** e **entre** usando as credenciais de sua conta do Azure.

1. Depois de entrar com êxito, feche a nova janela do navegador e volte para o Visual Studio Code.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. Publicar o projeto no Azure

A primeira implantação do código inclui a criação de um recurso de Função em sua assinatura do Azure.

1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions**, escolha o botão **Implantar no aplicativo de funções...** .

    ![Publicar seu projeto no Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos prompts:

    + **Selecionar pasta**: escolha a pasta que contém o aplicativo de funções. 

    + **Selecione a assinatura**: Escolha a assinatura a ser usada. Essa opção não será exibida caso você possua apenas uma assinatura.

    + **Selecione o aplicativo de funções no Azure**: Escolha `Create new Function App`.

    + **Insira um nome globalmente exclusivo para o aplicativo de funções**: digite um nome que seja exclusivo em todo o Azure em um caminho de URL. O nome digitado é validado para garantir a exclusividade global.

    - **Selecione uma localização para novos recursos**:  Para obter um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de você.

1. Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. Selecione **Exibir Saída** para ver os resultados da criação e da implantação.

    ![Criar notificação completa](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

<br/>

<details>
<summary><strong>Não consegue publicar a função?</strong></summary>

Esta seção criou os recursos do Azure e implantou o código local no aplicativo de funções. Se isso não foi executado com sucesso:

* Examine a saída para obter informações sobre o erro. O ícone de sino no canto inferior direito é outra maneira de ver a saída. 
* Você publicou em um aplicativo de funções existente? Essa ação substitui o conteúdo do aplicativo no Azure.
</details>

<br/>

<details>
<summary><strong>Quais recursos foram criados?</strong></summary>

Quando concluído, os seguintes recursos do Azure serão criados em sua assinatura, usando nomes baseados em seu nome do aplicativo de funções:

* **Grupo de recursos**: um grupo de recursos é um contêiner lógico para recursos relacionados na mesma região.
* **Conta de Armazenamento do Azure**: um recurso de armazenamento mantém o estado e outras informações sobre seu projeto.
* **Plano de consumo**: um plano de consumo define o host subjacente para o aplicativo de funções sem servidor.
* **Aplicativo de funções**: um aplicativo de funções fornece o ambiente para executar o código da função e as funções de grupo como uma unidade lógica.
* **Application Insights**: o Application Insights controla o uso da função sem servidor.

</details>

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. Executar a função no Azure

1. De volta na área **Azure: Funções** na barra lateral, expanda sua assinatura, o novo aplicativo de funções e **Funções**. Clique com o botão direito do mouse (Windows) ou clique em <kbd>CTRL -</kbd> (macOS) na função `HttpExample` e escolha **Função Executar Agora...** .

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar a função agora no Azure por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`. Clique em ENTER para enviar essa mensagem de solicitação à função.  

1. Quando a função é executada no Azure e retorna uma resposta, uma notificação é gerada no Visual Studio Code.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. Limpar os recursos

Se você não planeja prosseguir para a [próxima etapa](#next-steps), exclua o aplicativo de funções e seus recursos para evitar incorrer em custos adicionais.

1. No Visual Studio Code, selecione o ícone do Azure na barra de Atividade e selecione a área Funções na barra lateral.
1. Selecione o aplicativo de funções, clique com o botão direito do mouse e selecione **Excluir Aplicativo de funções...** .

<br/>
<hr/>

## <a name="next-steps"></a>Próximas etapas

Expanda a função adicionando uma <abbr title="No Armazenamento do Azure, uma forma de associar uma função a uma fila de armazenamento para que ela possa criar mensagens na fila.">associação de saída</abbr>. Essa associação grava a cadeia de caracteres da solicitação HTTP em uma mensagem em uma fila do Armazenamento de Filas do Azure.

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
