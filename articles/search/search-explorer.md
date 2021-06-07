---
title: Ferramenta de consulta do gerenciador de pesquisa no portal do Azure
titleSuffix: Azure Cognitive Search
description: Neste início rápido do portal do Azure, use o Gerenciador de Pesquisa para aprender sintaxe de consulta, testar expressões de consulta ou inspecionar um documento de pesquisa. O Gerenciador de pesquisa consulta índices no Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: e9607a71ed6b045ac704c43bf4ea54c9f181bbf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179768"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Início Rápido: Usar o Gerenciador de pesquisa para executar consultas no portal

O **Gerenciador de pesquisa** é uma ferramenta de consulta interna usada para executar consultas em um índice de pesquisa no Azure Cognitive Search. Essa ferramenta facilita a aprendizagem da sintaxe de consulta, o teste de uma expressão de consulta ou filtro ou a confirmação de uma atualização de dados verificando se o novo conteúdo existe no índice.

Este guia de início rápido usa o índice existente para demonstrar o Gerenciador de pesquisa. As solicitações são formuladas usando a [API REST de Pesquisa](/rest/api/searchservice/search-documents), com respostas retornadas como documentos JSON detalhados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, os seguintes pré-requisitos precisam estar em vigor:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).

+ Um serviço do Azure Cognitive Search. [Crie um serviço](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido. 

+ O *realestate-us-sample-index* é usado para este guia de início rápido. Use o [Início rápido: Criar um índice](search-import-data-portal.md) para criar o índice usando valores padrão. Uma fonte de dados de exemplo interna hospedada pela Microsoft (**realestate-us-sample**) fornece os dados.

## <a name="start-search-explorer"></a>Iniciar o Gerenciador de pesquisa

1. No [portal do Azure](https://portal.azure.com), abra a página de visão geral de pesquisa no painel ou [localize seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Abra o Gerenciador de pesquisa por meio da barra de comandos:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Comando do gerenciador de pesquisa no portal" border="true":::

    Ou use a guia inserida do **Gerenciador de pesquisa** em um índice aberto:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Guia do Gerenciador de pesquisa" border="true":::

## <a name="unspecified-query"></a>Consulta não especificada

Na primeira observação do conteúdo, execute uma pesquisa vazia clicando em **Pesquisar** sem nenhum termo fornecido. Uma pesquisa vazia é útil como uma primeira consulta porque ela retorna documentos inteiros para que você possa examinar a composição do documento. Em uma pesquisa vazia, não há nenhuma classificação de pesquisa e os documentos são retornados em ordem arbitrária (`"@search.score": 1` para todos os documentos). Por padrão, são retornados 50 documentos em uma solicitação de pesquisa.

Uma sintaxe equivalente para uma pesquisa vazia é `*` ou `search=*`.
   
   ```http
   search=*
   ```

   **Resultados**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Exemplo de consulta vazia ou não qualificada" border="true":::

## <a name="free-text-search"></a>Pesquisa de texto livre

Consultas de forma livre, com ou sem operadores, são úteis para simular consultas definidas pelo usuário enviadas de um aplicativo personalizado para o Azure Cognitive Search. Somente esses campos atribuídos como **Pesquisáveis** na definição do índice são verificados quanto a correspondências. 

Observe que, quando você fornece critérios de pesquisa, como expressões ou termos de consulta, a classificação de pesquisa entra em cena. O exemplo a seguir ilustra uma pesquisa de texto livre.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Você pode usar Ctrl-F para pesquisar termos específicos de interesse nos resultados.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Exemplo de consulta de texto livre" border="true":::

## <a name="count-of-matching-documents"></a>Contagem de documentos correspondentes 

Adicione **$count=true** para obter o número de correspondências encontradas em um índice. Em uma pesquisa vazia, a contagem é o número total de documentos no índice. Em uma pesquisa qualificada, é o número de documentos que correspondem à entrada da consulta. Lembre-se de que o serviço retorna as principais 50 correspondências por padrão, então talvez você tenha mais correspondências no índice do que o que está incluído nos resultados.

   ```http
   $count=true
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Contagem de documentos correspondentes no índice" border="true":::

## <a name="limit-fields-in-search-results"></a>Limitar campos nos resultados da pesquisa

Adicione [ **$select**](search-query-odata-select.md) para limitar os resultados aos campos nomeados explicitamente para que a leitura fique mais legível no **Gerenciador de pesquisa**. Para manter a cadeia de caracteres de pesquisa e **$count=true**, coloque **&** como prefixo dos argumentos. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Restringir os campos nos resultados da pesquisa" border="true":::

## <a name="return-next-batch-of-results"></a>Retornar o próximo lote de resultados

O Azure Cognitive Search retorna as primeiras 50 correspondências com base na classificação de pesquisa. Para obter o próximo conjunto de documentos correspondentes, acrescente **$top=100,&$skip=50** para aumentar o conjunto de resultados para 100 documentos (o padrão é 50, o máximo é 1000), ignorando os primeiros 50 documentos. Você pode verificar a chave do documento (listingID) para identificar um documento. 

Lembre-se de que você precisa fornecer critérios de pesquisa, como um termo ou expressão de consulta, para obter os resultados classificados. Observe que as pontuações de pesquisa diminuem quanto mais você avança nos resultados da pesquisa.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Retornar o próximo lote de resultados da pesquisa" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expressões de filtro (maior que, menor que, igual a)

Use o parâmetro [ **$filter**](search-query-odata-filter.md) quando desejar especificar critérios precisos em vez da pesquisa de texto livre. O campo deve ser atribuído como **Filtrável** no índice. Este exemplo pesquisa quartos maiores que 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Filtrar por critérios" border="true":::

## <a name="order-by-expressions"></a>Expressões orderby

Adicione [ **$orderby**](search-query-odata-orderby.md) para classificar os resultados por outro campo além da pontuação de pesquisa. O campo deve ser atribuído como **Classificável** no índice. Uma expressão de exemplo que você pode usar para testar isso é:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Alterar a ordem de classificação" border="true":::

As expressões **$filter** e **$orderby** são construções do OData. Para saber mais, confira [Sintaxe de filtro OData](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Observações

Neste início rápido, você usou o **Gerenciador de pesquisa** para consultar um índice usando a API REST.

+ Os resultados são retornados como documentos JSON detalhados para que você possa exibir o conteúdo e a construção do documento na totalidade. O parâmetro **$select** em uma expressão de consulta pode limitar quais campos são retornados.

+ Os documentos contêm todos os campos marcados como **Recuperáveis** no índice. Para exibir atributos de índice no portal, clique em *realestate-us-sample* na lista **Índices** na página de visão geral da pesquisa.

+ As consultas de forma livre, semelhantes ao que você pode digitar em um navegador da Web comercial, são úteis para testar uma experiência do usuário final. Por exemplo, supondo o índice de exemplo realestate interno, você poderia digitar "Apartamentos Seattle lago washington" e, em seguida, usar Ctrl-F para localizar termos nos resultados da pesquisa. 

+ As expressões de consulta e de filtro são articuladas em uma sintaxe implementada pelo Azure Cognitive Search. O padrão é uma [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search), mas você pode opcionalmente usar uma [Lucene completa](/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais poderosas. [Expressões de filtro](/rest/api/searchservice/odata-expression-syntax-for-azure-search) são uma sintaxe de OData.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre estruturas de consulta e sintaxe, use o Postman ou uma ferramenta equivalente para criar expressões de consulta que utilizem mais partes da API. A [API REST de Pesquisa](/rest/api/searchservice/search-documents) é útil principalmente para aprendizado e exploração.

> [!div class="nextstepaction"]
> [Criar uma consulta básica no Postman](search-get-started-rest.md)