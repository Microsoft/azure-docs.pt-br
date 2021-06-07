---
title: 'Tutorial de JavaScript: Visão geral da integração de Pesquisa'
titleSuffix: Azure Cognitive Search
description: Visão geral técnica e configuração para adicionar a pesquisa a um site e implantá-lo em um Aplicativo Web Estático do Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723431"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 – Visão geral da adição da pesquisa a um site

Este tutorial cria um site de pesquisa em um catálogo de livros e implanta-o em um Aplicativo Web Estático do Azure. 

O aplicativo está disponível: 
* [Amostra](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Site de demonstração – aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>O que o exemplo faz? 

Este site de exemplo fornece acesso a um catálogo de dez mil livros. Um usuário pode pesquisar o catálogo inserindo um texto na barra de pesquisa. Enquanto o usuário digita o texto, o site usa o recurso de sugestão do Índice de Pesquisa para completá-lo. Depois que a consulta é concluída, a lista de livros é exibida com uma parte dos detalhes. Um usuário pode escolher um livro para ver todos os detalhes do livro, armazenados no Índice de Pesquisa. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Este site de exemplo fornece acesso a um catálogo de dez mil livros. Um usuário pode pesquisar o catálogo inserindo um texto na barra de pesquisa. Enquanto o usuário digita o texto, o site usa o recurso de sugestão do Índice de Pesquisa para completá-lo. Depois que a pesquisa é concluída, a lista de livros é exibida com uma parte dos detalhes. Um usuário pode escolher um livro para ver todos os detalhes do livro, armazenados no Índice de Pesquisa.":::

A experiência de pesquisa inclui: 

* Pesquisa: fornece a funcionalidade de pesquisa para o aplicativo.
* Sugestão: fornece sugestões enquanto o usuário digita um texto na barra de pesquisa.
* Pesquisa de documento: procura um documento pela ID para recuperar todo o conteúdo dele na página de detalhes.

## <a name="how-is-the-sample-organized"></a>Como o exemplo é organizado?

O [exemplo](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) inclui o seguinte:

|Aplicativo|Finalidade|GitHub<br>Repositório<br>Location|
|--|--|--|
|Cliente|Aplicativo React (camada de apresentação) para exibir livros, com a pesquisa. Ele chama o aplicativo de funções do Azure. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Servidor|Aplicativo de funções do Azure (camada de negócios): chama a API do Azure Cognitive Search usando o SDK do JavaScript |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Bulk Insert|Arquivo JavaScript para criar o índice e adicionar documentos a ele.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Instale os recursos a seguir no ambiente de desenvolvimento local. 

- [Node.js 12 ou 14](https://nodejs.org/en/download)
    - Se você tiver outra versão do Node.js instalada no computador local, considere a possibilidade de usar o [nvm](https://github.com/nvm-sh/nvm) (Node Version Manager) ou um contêiner do Docker.  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) e as extensões a seguir
    - [Recursos do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure Cognitive Search 0.2.0 e posterior](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Aplicativo Web Estático do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Opcional:
    - Este tutorial não executa a API da Função do Azure localmente, mas se você pretende executá-la localmente, instale o [azure-functions-core-tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) globalmente com o seguinte comando do Bash: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Criar um fork e clonar o exemplo de pesquisa com o Git

A criação de um fork do repositório de exemplos é crítica para a implantação do Aplicativo Web Estático. Os aplicativos Web determinam as ações de build e o conteúdo de implantação com base na localização do seu fork do GitHub. A execução de código no Aplicativo Web Estático é remota, com os Aplicativos Web Estáticos do Azure fazendo a leitura do código no seu exemplo com fork.

1. No GitHub, crie um fork do [repositório de exemplos](https://github.com/Azure-Samples/azure-search-javascript-samples). 

    Conclua o processo de fork no navegador da Web com a sua conta do GitHub. Este tutorial usa seu fork como parte da implantação em um Aplicativo Web Estático do Azure. 

1. Em um terminal do Bash, baixe o aplicativo de exemplo no computador local. 

    Substitua `YOUR-GITHUB-ALIAS` pelo seu alias do GitHub. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. No Visual Studio Code, abra a pasta local do repositório clonado. As tarefas restantes são executadas no Visual Studio Code, a menos que especificado.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Criar um grupo de recursos para seus recursos do Azure

1. No Visual Studio Code, abra a [Barra de atividades](https://code.visualstudio.com/docs/getstarted/userinterface) e selecione o ícone do Azure. 
1. Na barra lateral, **clique com o botão direito do mouse na sua assinatura do Azure** na área `Resource Groups` e selecione **Criar grupo de recursos**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Na barra lateral, **clique com o botão direito do mouse na sua assinatura do Azure** na área 'Grupos de Recursos' e selecione **Criar grupo de recursos**.":::
1. Insira um nome de grupo de recursos, como `cognitive-search-website-tutorial`. 
1. Escolha uma localização perto de você.
1. Quando você criar os recursos do Cognitive Search e Aplicativo Web Estático, mais adiante no tutorial, use esse grupo de recursos. 

    A criação de um grupo de recursos fornece uma unidade lógica para gerenciar os recursos, incluindo a exclusão deles quando você terminar de usá-los.

## <a name="next-steps"></a>Próximas etapas

* [Criar um Índice de Pesquisa e carregá-lo com documentos](tutorial-javascript-create-load-index.md)
* [Implantar seu Aplicativo Web Estático](tutorial-javascript-deploy-static-web-app.md)
