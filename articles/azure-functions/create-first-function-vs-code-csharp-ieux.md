---
title: Criar uma função C# usando o Visual Studio Code – Azure Functions
description: Saiba como criar uma função C# e publicar o projeto local por meio da hospedagem sem servidor no Azure Functions usando a extensão do Azure Functions no Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121024"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Início rápido: criar uma função C# no Azure usando o Visual Studio Code

> [!div class="op_single_selector" title1="Selecione a linguagem da função: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Outro (Go/Rust)](create-first-function-vs-code-other.md)

Neste artigo, você usará o Visual Studio Code para criar uma função baseada em biblioteca de classes C# que responde a solicitações HTTP. Após testar o código localmente, você o implanta no <abbr title="Um ambiente de computação de runtime no qual todos os detalhes do servidor são transparentes para os desenvolvedores de aplicativos, simplificando o processo de implantação e gerenciamento de código.">sem servidor</abbr> ambiente do <abbr title="O serviço do Azure que fornece um ambiente de computação sem servidor de baixo custo para os aplicativos.">Funções do Azure</abbr>.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Também há uma [versão baseada na CLI](create-first-function-cli-csharp.md) deste artigo.
    
## <a name="1-configure-your-environment"></a>1. Configurar seu ambiente

Antes de começar, verifique se você tem os seguintes requisitos implementados:

+ Uma conta do Azure <abbr title="O perfil que mantém as informações de cobrança pelo uso do Azure.">account</abbr> com uma assinatura ativa <abbr title="A estrutura organizacional básica na qual você gerencia recursos no Azure, normalmente associada a um indivíduo ou departamento da organização.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools), versão 3.x.

+ [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [Extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code.  

+ A [Extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. Criar seu projeto local

Nesta seção, você usará o Visual Studio Code para criar um <abbr title="Um contêiner lógico para uma ou mais funções individuais que podem ser implantadas e gerenciadas juntas.">projeto local do Azure Functions</abbr> em C#. Mais adiante neste artigo, você publicará o código de função no Azure.

1. Escolha o ícone do Azure na <abbr title="O grupo vertical de ícones no lado esquerdo da janela do Visual Studio Code.">Barra de atividades</abbr>Em seguida, na área **Azure: Functions**, selecione o ícone **Criar projeto...** .

    ![Escolher Criar um projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o workspace do projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estas etapas foram projetadas para serem concluídas fora de um workspace. Nesse caso, não selecione uma pasta de projeto que faz parte de um workspace.

1. Forneça as seguintes informações nos prompts:

    + **Selecione uma linguagem de programação para o seu projeto de função**: Escolha `C#`.

    + **Selecione um modelo para a primeira função do projeto**: Escolha `HTTP trigger`.

    + **Forneça um nome de função**: Digite `HttpExample`.

    + **Forneça um namespace**: Digite `My.Functions`.

    + **Nível de autorização**: Escolha `Anonymous`, que permite que qualquer pessoa chame seu ponto de extremidade de função. Para saber mais sobre os níveis de autorização, consulte [Chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como você gostaria de abrir seu projeto**: Escolha `Add to workspace`.

1. Usando essas informações, o Visual Studio Code gera um projeto do Azure Functions com um gatilho HTTP <abbr title="O tipo de evento que invoca o código da função, por exemplo, uma solicitação HTTP, uma mensagem da fila ou uma hora específica.">gatilho</abbr>. Você pode exibir os arquivos de projeto locais no Explorer. Para saber mais sobre os arquivos criados, confira [Arquivos de projeto gerados](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Após verificar se a função foi executada corretamente no computador local, é hora de usar o Visual Studio Code para publicar o projeto diretamente no Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. Publicar o projeto no Azure

Nesta seção, você criará um aplicativo de funções e os recursos relacionados em sua assinatura do Azure e, em seguida, implantará seu código. 

> [!IMPORTANT]
> Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure. 

1. Escolha o ícone do Azure na Barra de atividade e, em seguida, na área **Azure: Functions**, escolha o botão **Implantar no aplicativo de funções...** .

    

1. Forneça as seguintes informações nos prompts:

    + **Selecione a pasta**: escolha uma pasta do seu workspace ou navegue até uma que contenha seu aplicativo de funções. Você não verá isso se já tiver um aplicativo de funções válido aberto.

    + **Selecione a assinatura**: Escolha a assinatura a ser usada. Essa opção não será exibida caso você possua apenas uma assinatura.

    + **Selecione o aplicativo de funções no Azure**: Escolha `+ Create new Function App`. (Não escolha a opção `Advanced`, que não é abordada neste artigo.)

    + **Insira um nome exclusivo globalmente para o aplicativo de funções**: Digite um nome que seja válido em um caminho de URL. O nome digitado é validado para garantir que seja <abbr title="O nome deve ser exclusivo entre todos os projetos do Functions usados por todos os clientes do Azure globalmente. Normalmente, você usa uma combinação de seu nome ou do nome da empresa, o nome do aplicativo e um identificador numérico, por exemplo, contoso-bizapp-func-20">exclusivo no Azure Functions</abbr>. 

    + **Selecione uma localização para novos recursos**:  Para obter um melhor desempenho, escolha uma [região](https://azure.microsoft.com/regions/) perto de você.

    A extensão mostra o status de recursos individuais conforme eles são criados no Azure na área de notificação.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notificação de criação de recurso do Azure":::

1. Quando concluído, os seguintes recursos do Azure serão criados em sua assinatura, usando nomes baseados em seu nome do aplicativo de funções:

    + Um **grupo de recursos**, que é um contêiner lógico para recursos relacionados.
    + Uma **conta de armazenamento do Azure** padrão, que mantém o estado e outras informações sobre seus projetos.
    + Um **plano de consumo**, que define o host subjacente para o aplicativo de funções sem servidor. 
    + Um **aplicativo de funções**, que fornece o ambiente para a execução do código de função. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos dentro do mesmo plano de hospedagem.
    + Uma **instância do Application Insights** conectada ao aplicativo de funções, que controla o uso de sua função sem servidor.

    Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. 

    > [!TIP]
    > Por padrão, os recursos do Azure necessários para o aplicativo de funções são criados com base no nome do aplicativo de funções fornecido por você. Por padrão, eles também são criados no mesmo grupo de recursos com o aplicativo de funções. Se desejar personalizar os nomes desses recursos ou reutilizar os recursos existentes, você precisará [publicar o projeto com opções de criação avançadas](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).


1. Escolha **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure que você criou. Se você perder a notificação, selecione o ícone de sino no canto inferior direito para vê-lo novamente.

    ![Criar notificação completa](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. Executar a função no Azure

1. De volta na área **Azure: Funções** na barra lateral, expanda sua assinatura, o novo aplicativo de funções e **Funções**. Clique com o botão direito do mouse (Windows) ou clique em <kbd>CTRL -</kbd> (macOS) na função `HttpExample` e escolha **Função Executar Agora...** .

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar a função agora no Azure por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`.

    Clique em ENTER para enviar essa mensagem de solicitação à função.  

1. Quando a função é executada no Azure e retorna uma resposta, uma notificação é gerada no Visual Studio Code.

## <a name="5-clean-up-resources"></a>5. Limpar os recursos

Quando prosseguir para a [próxima etapa](#next-steps) e adicionar uma <abbr title="Uma forma de associar uma função a uma fila de armazenamento para que ela possa criar mensagens na fila."> Associação de saída de fila do Armazenamento do Azure</abbr> à sua função, você precisará manter todos os seus recursos no local para dar continuidade ao que já fez.

Caso contrário, você poderá usar as etapas a seguir para excluir o aplicativo de funções e recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Para saber mais sobre os custos do Functions, confira [Estimar os custos do plano de consumo](functions-consumption-costs.md).

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio Code para criar um aplicativo de funções com uma função disparada por HTTP simples. No próximo artigo, você pode expandir essa função adicionando uma associação de saída <abbr title="Uma conexão declarativa entre uma função e outros recursos. Uma associação de entrada fornece dados à função; uma associação de saída fornece dados da função para outros recursos.">associação</abbr>. Essa associação grava a cadeia de caracteres da solicitação HTTP em uma mensagem em uma fila do Armazenamento de Filas do Azure. 

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
