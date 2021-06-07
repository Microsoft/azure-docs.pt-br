---
title: 'Tutorial de JavaScript: Implantar o site habilitado para pesquisa'
titleSuffix: Azure Cognitive Search
description: Implante o site habilitado para pesquisa no Aplicativo Web Estático do Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723435"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 – Implantar o site habilitado para pesquisa

Implante o site habilitado para pesquisa como um Aplicativo Web Estático do Azure. Essa implantação inclui o aplicativo React e o aplicativo de funções.  

O Aplicativo Web Estático efetua pull das informações e dos arquivos para implantação do GitHub usando seu fork do repositório de exemplos.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Criar um Aplicativo Web Estático no Visual Studio Code

1. Selecione **Azure** na barra de atividades e **Aplicativos Web Estáticos** na barra lateral. 
1. Clique com o botão direito do mouse no nome da assinatura e selecione **Criar Aplicativo Web Estático (Avançado)** .    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Clique com o botão direito do mouse no nome da assinatura e selecione **Criar Aplicativo Web Estático (Avançado)**.":::

1. Siga os avisos para fornecer as seguintes informações:

    |Prompt|Digite|
    |--|--|
    |Como você deseja criar um Aplicativo Web Estático?|Usar um repositório GitHub existente|
    |Escolher a organização|Selecione um alias _próprio_ do GitHub como a organização.|
    |Escolher o repositório|Selecione **azure-search-javascript-samples** na lista. |
    |Escolher o branch do repositório|Selecione **mestre** na lista. |
    |Insira o nome para o novo Aplicativo Web Estático.|Crie um nome exclusivo para seu recurso. Por exemplo, você pode colocar seu nome antes do nome do repositório, como, `joansmith-azure-search-javascript-samples`. |
    |Selecione um grupo de recursos para novos recursos.|Use o grupo de recursos criado para este tutorial.|
    |Escolha a predefinição de build para configurar a estrutura de projeto padrão.|Selecionar **Personalizado**|
    |Selecionar a localização do código do aplicativo|`search-website`|
    |Selecionar a localização do código da Função do Azure|`search-website/api`|
    |Inserir o caminho da saída do build...|compilar|
    |Selecione uma localização para novos recursos.|Selecione uma região próxima a você.|

1. O recurso foi criado. Selecione **abrir Ações no GitHub** nas Notificações. Isso abrirá uma janela do navegador apontada para seu repositório com fork. 

    A lista de ações indica que o seu aplicativo Web, o cliente e as funções, foram enviados com sucesso para seu Aplicativo Web Estático do Azure. 

    Aguarde até o build e a implantação serem concluídos antes de continuar. Isso poderá levar alguns minutos para ser concluído.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Obter a chave de consulta do Cognitive Search no Visual Studio Code

1. No Visual Studio Code, abra a [Barra de atividades](https://code.visualstudio.com/docs/getstarted/userinterface) e selecione o ícone do Azure. 

1. Na barra lateral, selecione sua assinatura do Azure na área **Azure: Cognitive Search**, clique com o botão direito do mouse no recurso de Pesquisa e escolha **Copiar Chave de Consulta**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Na barra lateral, selecione sua assinatura do Azure na área **Azure: Cognitive Search**, clique com o botão direito do mouse no recurso de Pesquisa e escolha **Copiar Chave de Consulta**.":::

1. Mantenha essa chave de consulta, pois você precisará usá-la na próxima seção. Ela pode consultar o Índice. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Adicionar definições de configuração no Visual Studio Code

O aplicativo de funções do Azure não retornará dados de pesquisa até que os segredos da Pesquisa estejam nas configurações. 

1. Selecione **Azure** na barra de atividades e **Aplicativos Web Estáticos** na barra lateral. 
1. Expanda seu novo Aplicativo Web Estático até que as **Configurações de Aplicativo** sejam exibidas.
1. Clique com o botão direito do mouse em **Configurações de Aplicativo** e selecione **Adicionar Nova Configuração**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Clique com o botão direito do mouse em **Configurações de Aplicativo** e selecione **Adicionar Nova Configuração**.":::

1. Adicione as seguintes configurações:

    |Configuração|O valor do recurso de Pesquisa|
    |--|--|
    |SearchApiKey|A chave de consulta de Pesquisa|
    |SearchServiceName|O nome do recurso de Pesquisa|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Usar a pesquisa no seu Aplicativo Web Estático

1. No Visual Studio Code, abra a [Barra de atividades](https://code.visualstudio.com/docs/getstarted/userinterface) e selecione o ícone do Azure.
1. Na barra lateral, **clique com o botão direito do mouse na sua assinatura do Azure** na área `Static web apps` e localize o Aplicativo Web Estático criado para este tutorial.
1. Clique com o botão direito do mouse no nome do Aplicativo Web Estático e selecione **Procurar site**.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Clique com o botão direito do mouse no nome do Aplicativo Web Estático e selecione **Procurar site**.":::    

1. Selecione **Abrir** na caixa de diálogo pop-up.
1. Na barra de pesquisa do site, insira uma consulta de pesquisa como `code`, _lentamente_, para que o recurso de sugestão sugira títulos de livros. Selecione uma sugestão ou continue inserindo sua consulta. Clique em ENTER quando concluir a consulta de pesquisa. 
1. Examine os resultados e escolha um dos livros para ver mais detalhes. 

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados neste tutorial, exclua o grupo de recursos.

1. No Visual Studio Code, abra a [Barra de atividades](https://code.visualstudio.com/docs/getstarted/userinterface) e selecione o ícone do Azure. 

1. Na barra lateral, **clique com o botão direito do mouse na sua assinatura do Azure** na área `Resource Groups` e localize o grupo de recursos criado para este tutorial.
1. Clique com o botão direito do mouse no nome do grupo de recursos e selecione **Excluir**.
    Isso excluirá os recursos de Aplicativo Web Estático e de Pesquisa.
1. Se não quiser mais o fork do GitHub do exemplo, lembre-se de excluí-lo no GitHub. Acesse as **Configurações** do fork e exclua-o. 


## <a name="next-steps"></a>Próximas etapas

* [Noções básicas sobre a integração de Pesquisa ao site habilitado para pesquisa](tutorial-javascript-search-query-integration.md)
