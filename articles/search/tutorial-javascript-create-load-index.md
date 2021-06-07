---
title: 'Tutorial de JavaScript: Adicionar a pesquisa a aplicativos Web'
titleSuffix: Azure Cognitive Search
description: Crie um índice e importe dados CSV para o índice de Pesquisa com JavaScript usando o SDK do npm @azure/search-documents.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723450"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2 – Criar e carregar o índice de Pesquisa com JavaScript

Continue criando seu site habilitado para pesquisa fazendo o seguinte:
* Criar um recurso de Pesquisa com a extensão do VS Code
* Criar um índice e importar dados com JavaScript usando o script de exemplo e o SDK do Azure [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents).

## <a name="create-an-azure-search-resource"></a>Criar um recurso do Azure Search 

Crie um recurso de Pesquisa com a extensão do [Azure Cognitive Search](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) para Visual Studio Code.

1. No Visual Studio Code, abra a [Barra de atividades](https://code.visualstudio.com/docs/getstarted/userinterface) e selecione o ícone do Azure. 

1. Na barra lateral, **clique com o botão direito do mouse na sua assinatura do Azure** na área `Azure: Cognitive Search` e selecione **Criar serviço de pesquisa**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Na barra lateral, clique com o botão direito do mouse na sua assinatura do Azure na área **Azure: Cognitive Search** e selecione **Criar serviço de pesquisa**.":::

1. Siga os avisos para fornecer as seguintes informações:

    |Prompt|Digite|
    |--|--|
    |Insira um nome globalmente exclusivo para o novo Serviço de Pesquisa.|**Lembre-se desse nome**. Esse nome de recurso se tornará uma parte do ponto de extremidade do recurso.|
    |Selecionar um grupo de recursos para novos recursos|Use o grupo de recursos criado para este tutorial.|
    |Selecione o SKU para o Serviço de Pesquisa.|Selecione **Gratuito** para este tutorial. Não é possível alterar um tipo de preço de SKU depois que o serviço é criado.|
    |Selecione uma localização para novos recursos.|Selecione uma região próxima a você.|

1. Depois que você concluir os avisos, o recurso de Pesquisa será criado. 

## <a name="get-your-search-resource-admin-key"></a>Obter sua chave de administrador do recurso de Pesquisa

Obtenha sua chave de administrador do recurso de Pesquisa com a extensão do Visual Studio Code. 

1. No Visual Studio Code, na barra lateral, clique com o botão direito do mouse no recurso de Pesquisa e selecione **Copiar Chave de Administrador**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Na barra lateral, clique com o botão direito do mouse no recurso de Pesquisa e selecione **Copiar Chave de Administrador**.":::

1. Mantenha essa chave de administrador, pois você precisará usá-la em [uma seção posterior](#prepare-the-bulk-import-script-for-search). 

## <a name="prepare-the-bulk-import-script-for-search"></a>Preparar o script de importação em massa para a Pesquisa

O script usa o SDK do Azure para Cognitive Search:

* [pacote npm @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Documentação de referência](/javascript/api/overview/azure/search-documents-readme)

1. No Visual Studio Code, abra o arquivo `bulk_insert_books.js` no subdiretório `search-web/bulk-insert` e substitua as seguintes variáveis por valores próprios para se autenticar no SDK do Azure Search:

    * NOME-DO-RECURSO-DE-PESQUISA
    * CHAVE-DE-ADMINISTRADOR-DE-PESQUISA

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Abra um terminal integrado no Visual Studio para o subdiretório do diretório do projeto, `search-web/bulk-insert`, e execute o comando a seguir para instalar as dependências. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Executar o script de importação em massa para a Pesquisa

1. Continue usando o terminal integrado no Visual Studio para o subdiretório do diretório do projeto, `search-web/bulk-insert`, para executar o seguinte comando do Bash para executar o script `bulk_insert_books.js`:

    ```javascript
    npm start
    ```

1. À medida que o código é executado, o console exibe o progresso. 
1. Quando o upload for concluído, a última instrução impressa no console será "Concluído".

## <a name="review-the-new-search-index"></a>Examinar o novo índice de Pesquisa

Quando o upload for concluído, o índice de Pesquisa estará pronto para uso. Examine o novo Índice.

1. No Visual Studio Code, abra a extensão do Azure Cognitive Search e selecione o recurso de Pesquisa.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="No Visual Studio Code, abra a extensão do Azure Cognitive Search e abra o recurso de Pesquisa.":::

1. Expanda Índices, Documentos e `good-books` e selecione um documento para ver todos os dados específicos do documento.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Expanda Índices, 'good-books' e selecione um documento.":::

## <a name="copy-your-search-resource-name"></a>Copiar o nome do recurso de Pesquisa

Anote o **nome do recurso de Pesquisa**. Você precisará dele para conectar o aplicativo de funções do Azure ao recurso de Pesquisa. 

> [!CAUTION]
> Embora você possa ter a tentação de usar sua chave de administrador de Pesquisa na Função do Azure, isso não segue o princípio de privilégios mínimos. A Função do Azure usará a chave de consulta para estar em conformidade com o privilégio mínimo. 

## <a name="next-steps"></a>Próximas etapas

[Implantar seu Aplicativo Web Estático](tutorial-javascript-deploy-static-web-app.md)