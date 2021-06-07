---
title: Criar um aplicativo de demonstração no portal do Azure
titleSuffix: Azure Cognitive Search
description: Execute o assistente para Criar aplicativo de demonstração (versão prévia) para gerar páginas HTML e script para um aplicativo Web operacional. A página inclui uma barra de pesquisa, uma área de resultados, uma barra lateral e suporte para typeahead.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 590afe4c396942c5179826cd831908e37f48c3e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745743"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Início Rápido: Criar um aplicativo de demonstração no portal (Azure Cognitive Search)

Use o assistente **Criar aplicativo de demonstração** do portal do Azure para gerar um aplicativo Web no estilo "localhost" para download que seja executado em um navegador. Dependendo de sua configuração, o aplicativo gerado está operacional no primeiro uso, com uma conexão somente leitura dinâmica com um índice remoto. Um aplicativo padrão pode incluir uma barra de pesquisa, uma área de resultados, filtros de barra lateral e suporte para typeahead.

O aplicativo de demonstração pode ajudá-lo a visualizar como um índice funcionará em um aplicativo cliente, mas não se destina a cenários de produção. Os aplicativos cliente devem incluir segurança, tratamento de erro e lógica de hospedagem que a página HTML gerada não fornece. Quando você estiver pronto para criar um aplicativo cliente, confira [Criar seu primeiro aplicativo de pesquisa usando o SDK do .NET](tutorial-csharp-create-first-app.md) para as próximas etapas.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deverá ter o seguinte:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

+ Um serviço do Azure Cognitive Search. [Crie um serviço](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido. 

+ [Microsoft Edge (última versão)](https://www.microsoft.com/edge) ou Google Chrome.

+ Um [índice de pesquisa](search-what-is-an-index.md) para usar como a base do seu aplicativo gerado. 

  Este guia de início rápido usa o índice e os dados de exemplo internos de Imóveis porque ele tem imagens em miniatura (o assistente é compatível com a adição de imagens à página de resultados). Para criar o índice usado neste exercício, execute o assistente **Importar dados**, escolhendo a fonte de dados *realestate-us-sample*.

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="página fonte de dados para dados de exemplo" border="false":::

Quando o índice estiver pronto para uso, prossiga para a próxima etapa.

## <a name="start-the-wizard"></a>Iniciar o assistente

1. Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

1. [Localize seu serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) e, na página Visão Geral, nos links e no meio da página, selecione **Índices**. 

1. Escolha *realestate-us-sample-index* na lista de índices existentes.

1. Na página Índice, na parte superior, selecione **Criar aplicativo de demonstração (versão prévia)** para iniciar o assistente.

1. Na primeira página do assistente, selecione **Habilitar CORS (Compartilhamento de Recursos Entre Origens)** para adicionar suporte a CORS à definição de índice. Esta etapa é opcional, mas seu aplicativo Web local não se conectará ao índice remoto sem ela.

## <a name="configure-search-results"></a>Configurar resultados da pesquisa

O assistente fornece um layout básico para resultados da pesquisa renderizados que incluem espaço para uma imagem em miniatura, um título e uma descrição. O backup de cada um desses elementos é um campo no índice que fornece os dados. 

1. Em Miniatura, escolha o campo *miniatura* no índice *realestate-us-sample*. Este exemplo inclui miniaturas de imagem na forma de imagens endereçadas por URL armazenadas em um campo chamado *miniatura*. Caso seu índice não tenha imagens, deixe este campo em branco.

1. Em Título, escolha um campo que transmita a exclusividade de cada documento. Neste exemplo, a ID de listagem é uma seleção razoável.

1. Em Descrição, escolha um campo que dê detalhes para ajudar uma pessoa a decidir se ela deseja clicar nesse documento específico.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="configurar os resultados para os dados de exemplo" border="false":::

## <a name="add-a-sidebar"></a>Adicionar uma barra lateral

O serviço de pesquisa dá suporte à navegação facetada, que geralmente é renderizada como uma barra lateral. As facetas são baseadas em campos filtráveis e facetáveis, conforme expresso no esquema de índice.

No Azure Cognitive Search, a navegação facetada é uma experiência de filtragem cumulativa. Dentro de uma categoria, selecionar vários filtros expande os resultados (por exemplo, selecionar Seattle e Bellevue dentro de Cidade). Nas categorias, selecionar vários filtros reduz os resultados.

> [!TIP]
> Você pode exibir todo o esquema do índice no portal. Procure o link **definição do índice (JSON)** na página de visão geral de cada índice. Os campos que se qualificam para navegação facetada têm atributos "filtrable: true" e "facetable: true".

1. No assistente, selecione a guia **Barra lateral** na parte superior da página. Você verá uma lista de todos os campos atribuídos como filtráveis e com faceta no índice.

1. Aceite a seleção atual dos campos com faceta e prossiga para próxima página.

## <a name="add-typeahead"></a>Adicionar typeahead

A funcionalidade typeahead está disponível na forma de preenchimento automático e sugestões de consulta. O assistente dá suporte a sugestões de consulta. Com base nas entradas de pressionamento de teclas fornecidas pelo usuário, o serviço de pesquisa retorna uma lista de cadeias de consulta "concluídas" que podem ser selecionadas como entrada.

As sugestões são habilitadas em definições de campo específicas. O assistente fornece opções para configurar a quantidade de informações incluídas em uma sugestão. 

A captura de tela a seguir mostra as opções no assistente, justapostas a uma página renderizada no aplicativo. Você pode ver como as seleções de campo são usadas e como "Mostrar nome do campo" é usado para incluir ou excluir rótulos dentro da sugestão.

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="Configuração de sugestão de consulta":::

## <a name="add-suggestions"></a>Adicionar sugestões

As sugestões se referem a prompts de consulta automatizados que são anexados à caixa de pesquisa. O Cognitive Search dá suporte a dois deles: *preenchimento automático* de um termo de pesquisa inserido parcialmente e *sugestões* para uma lista suspensa de documentos potencialmente correspondentes.

O assistente dá suporte a sugestões, e os campos que podem fornecer resultados sugeridos são derivados de um constructo [`Suggesters`](index-add-suggesters.md) no índice:

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. No assistente, selecione a guia **Sugestões** na parte superior da página. Você verá uma lista de todos os campos designados no esquema de índice como provedores de sugestão.

1. Aceite a seleção atual e prossiga para próxima página.

## <a name="create-download-and-execute"></a>Criar, baixar e executar

1. Selecione **Criar aplicativo de demonstração** na parte inferior da página para gerar o arquivo HTML.

1. Quando solicitado, selecione **Baixar seu aplicativo** para baixar o arquivo.

1. Abra o arquivo e clique no botão Pesquisar. Essa ação executa uma consulta, que pode ser uma consulta vazia (`*`) que retorna um conjunto de resultados arbitrários. A página deve ser semelhante à captura de tela a seguir. Insira um termo e use filtros para restringir os resultados. 

O índice subjacente é composto por dados fictícios e gerados que foram duplicados em documentos, e as descrições às vezes não correspondem à imagem. Você pode esperar uma experiência mais coesa ao criar um aplicativo com base em seus próprios índices.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="Executar o aplicativo":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

O aplicativo de demonstração é útil para a criação de protótipos porque você pode simular uma experiência do usuário final sem precisar escrever o código JavaScript ou o código de front-end. Para obter mais informações sobre os recursos de front-end, comece com a faceted navigation:

> [!div class="nextstepaction"]
> [Como criar um filtro de faceta](search-filters-facets.md)