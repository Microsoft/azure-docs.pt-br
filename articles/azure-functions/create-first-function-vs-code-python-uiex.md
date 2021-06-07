---
title: Criar uma função Python usando o Visual Studio Code – Azure Functions
description: Saiba como criar uma função Python e publique o projeto local por meio da hospedagem sem servidor no Azure Functions usando a extensão do Azure Functions no Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031728"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Início rápido: criar uma função no Azure com o Python usando o Visual Studio Code

> [!div class="op_single_selector" title1="Selecione a linguagem da função: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Outro (Go/Rust)](create-first-function-vs-code-other.md)

Neste artigo, você usará o Visual Studio Code para criar uma função do Python que responde a solicitações HTTP. Após testar o código localmente, você o implanta no <abbr title="Um ambiente de computação de runtime no qual todos os detalhes do servidor são transparentes para os desenvolvedores de aplicativos, o que simplifica o processo de implantação e gerenciamento de código.">sem servidor</abbr> ambiente do <abbr title="Um serviço do Azure que fornece um ambiente de computação sem servidor de baixo custo para os aplicativos.">Funções do Azure</abbr>.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Também há uma [versão baseada na CLI](create-first-function-cli-python.md) deste artigo.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

Antes de começar, verifique se você tem os seguintes requisitos implementados:

+ Uma conta do Azure <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">account</abbr> com uma assinatura ativa <abbr title="A estrutura organizacional básica na qual você gerencia recursos no Azure, normalmente associada a um indivíduo ou departamento da organização.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools), versão 3.x.

+ Há suporte para o [Python 3.8](https://www.python.org/downloads/release/python-381/), o [Python 3.7](https://www.python.org/downloads/release/python-375/) e o [Python 3.6](https://www.python.org/downloads/release/python-368/) no Azure Functions (x64).

+ [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code.  

+ A [Extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a>Criar seu projeto local

1. Escolha o ícone do Azure na <abbr title="O grupo vertical de ícones no lado esquerdo da janela do Visual Studio Code.">Barra de atividades</abbr>Em seguida, na área **Azure: Functions**, selecione o ícone **Criar projeto...** .

    ![Escolher Criar um projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o workspace do projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estas etapas foram projetadas para serem concluídas fora de um workspace. Nesse caso, não selecione uma pasta de projeto que faz parte de um workspace.

1. Forneça as seguintes informações nos prompts:

    + **Selecione uma linguagem de programação para o seu projeto de função**: Escolha `Python`.

    + **Selecione um alias do Python para criar um ambiente virtual**: Escolha a localização do seu interpretador do Python. Se a localização não for mostrada, digite o caminho completo no binário do Python.  

    + **Selecione um modelo para a primeira função do projeto**: Escolha `HTTP trigger`.

    + **Forneça um nome de função**: Digite `HttpExample`.

    + **Nível de autorização**: Escolha `Anonymous`, que permite que qualquer pessoa chame seu ponto de extremidade de função. Para saber mais sobre os níveis de autorização, consulte [Chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como você gostaria de abrir seu projeto**: Escolha `Add to workspace`.

<br/>
<details>
<summary><strong>Não consegue criar um projeto de função?</strong></summary>

Os problemas mais comuns a serem resolvidos ao criar um projeto local no Functions são:
* Você não tem a extensão do Azure Functions instalada. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Pressione <kbd>F5</kbd> para iniciar o projeto do aplicativo de funções.

1. No painel **Terminal**, veja o ponto de extremidade da URL de sua função em execução no local.

    ![Saída do VS Code de função local](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Com o Core Tools em execução, acesse a área **Azure: Funções**. Em **Funções**, expanda **Projeto Local** > **Funções**. Clique com o botão direito do mouse (Windows) ou clique em <kbd>CTRL -</kbd> (macOS) na função `HttpExample` e escolha **Função Executar Agora...** .

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Executar a função agora por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`. Clique em ENTER para enviar essa mensagem de solicitação à função.  

1. Quando a função é executada localmente e retorna uma resposta, uma notificação é gerada no Visual Studio Code. As informações sobre a execução da função são mostradas no painel **Terminal**.

1. Pressione <kbd>Ctrl + C</kbd> para parar o Core Tools e desconectar o depurador.

<br/>
<details>
<summary><strong>Não consegue executar a função localmente?</strong></summary>

Os problemas mais comuns a serem resolvidos ao executar um projeto local no Functions são:
* Você não tem as Core Tools instaladas. 
*  Se você tiver problemas com a execução no Windows, verifique se o shell do terminal padrão do Visual Studio Code não está definido como **Bash WSL**. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Entrar no Azure

Para publicar o aplicativo, entre no Azure. Se você já tiver entrado, vá para a próxima seção.

1. Escolha o ícone do Azure na barra de Atividade e, na área **Azure: Functions**, escolha **Entrar no Azure...** .

    ![Entrar no Azure por meio do VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Quando solicitado no navegador, **escolha sua conta do Azure** e **entre** usando as credenciais de sua conta do Azure.

1. Depois de entrar com êxito, feche a nova janela do navegador e volte para o Visual Studio Code. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. Publicar o projeto no Azure

A primeira implantação do código inclui a criação de um recurso de Função em sua assinatura do Azure.

1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions**, escolha o botão **Implantar no aplicativo de funções...** .

    ![Publicar seu projeto no Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Forneça as seguintes informações nos prompts:

    + **Selecionar pasta**: escolha a pasta que contém o aplicativo de funções.

    + **Selecione a assinatura**: Escolha a assinatura a ser usada. Essa opção não será exibida caso você possua apenas uma assinatura.

    + **Selecione o aplicativo de funções no Azure**: Escolha `+ Create new Function App`.

    + **Insira um nome exclusivo globalmente para o aplicativo de funções**: Digite um nome que seja válido em um caminho de URL. O nome digitado é validado para garantir que seja <abbr title="O nome deve ser globalmente exclusivo entre todos os clientes do Azure. Por exemplo, você pode usar uma combinação de seu nome ou do nome da organização, o nome do aplicativo e um identificador numérico, por exemplo, contoso-bizapp-func-20.">exclusivo no Azure</abbr>. 

    + **Selecione um runtime**: Escolha a versão do Python em que você está executando localmente. É possível usar o comando `python --version` para verificar sua versão.

    + **Selecione um local para novos recursos**: para ter um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de você.

    A extensão mostra o status de recursos individuais conforme eles são criados no Azure na área de notificação.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificação de criação de recurso do Azure":::

1. Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. Selecione **Exibir Saída** para ver os resultados da criação e da implantação. 

    ![Criar notificação completa](./media/functions-create-first-function-vs-code/function-create-notifications.png)

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

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. Executar a função no Azure

1. Na barra lateral **Azure: Functions**, expanda o novo aplicativo de funções.
1. Expanda **Functions**, clique com o botão direito do mouse (Windows) ou <kbd>Ctrl -</kbd>, clique (macOS) na função `HttpExample` e escolha **Executar Função Agora...** .

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar a função agora no Azure por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`.

    Clique em ENTER para enviar essa mensagem de solicitação à função.  

1. Quando a função é executada no Azure e retorna uma resposta, uma notificação é gerada no Visual Studio Code.

## <a name="7-clean-up-resources"></a>7. Limpar os recursos

Quando prosseguir para a [próxima etapa](#next-steps) e adicionar uma <abbr title="Uma forma de associar uma função a uma fila de armazenamento para que ela possa criar mensagens na fila."> Associação de saída de fila do Armazenamento do Azure</abbr> à sua função, você precisará manter todos os seus recursos no local para dar continuidade ao que já fez.

Caso contrário, você poderá usar as etapas a seguir para excluir o aplicativo de funções e recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Para saber mais sobre os custos do Functions, confira [Estimar os custos do plano de consumo](functions-consumption-costs.md).

## <a name="next-steps"></a>Próximas etapas

Expandir a função adicionando uma saída <abbr title="Uma conexão declarativa entre uma função e outros recursos. Uma associação de entrada fornece dados à função; uma associação de saída fornece dados da função para outros recursos.">associação</abbr>. Essa associação grava a cadeia de caracteres da solicitação HTTP em uma mensagem em uma fila do Armazenamento de Filas do Azure. 

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Está com problemas? Fale conosco.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
