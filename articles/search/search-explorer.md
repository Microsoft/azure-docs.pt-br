---
title: Ferramenta de gerenciador de pesquisa para consultar dados no portal do Azure – Azure Search
description: Use ferramentas do portal do Azure, como o gerenciador Search para índices de consulta no Azure Search. Insira termos de pesquisa ou cadeias de caracteres de pesquisa totalmente qualificados com a sintaxe avançada.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 87e5ec82299ef9ddc8bc8756196bb2ace3d1f6f3
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414229"
---
# <a name="search-explorer-for-querying-data-in-azure-search"></a>Gerenciador de pesquisa para consultar dados no Azure Search 

Este artigo mostra como consultar um índice existente do Azure Search usando o **Search Explorer** no portal do Azure. Você pode usar o Search Explorer para enviar cadeias de caracteres de consulta Lucene simples ou completas para qualquer índice existente em seu serviço. 

   ![Comando do Gerenciador de pesquisa no portal](./media/search-explorer/search-explorer-cmd2.png "Comando Gerenciador de pesquisa no portal")


Para obter ajuda para começar, confira [Iniciar o gerenciador de pesquisa](#start-search-explorer).

## <a name="basic-search-strings"></a>Cadeias de caracteres de pesquisa básica

Os exemplos a seguir pressupõem o índice de exemplo realestate interno. Para obter ajuda para criar esse índice, confira [Início Rápido: Importar, índice e consulta no portal do Azure](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Exemplo 1 – pesquisa vazia

Para a primeira observação do seu código, execute uma pesquisa vazia clicando em **Pesquisar** sem nenhum termo fornecido. Uma pesquisa vazia é útil como uma primeira consulta porque ela retorna documentos inteiros para que você possa examinar a composição do documento. Em uma pesquisa vazia, não há nenhuma classificação de pesquisa e os documentos são retornados em ordem arbitrária (`"@search.score": 1` para todos os documentos). Por padrão, são retornados 50 documentos em uma solicitação de pesquisa.

Uma sintaxe equivalente para uma pesquisa vazia é `*` ou `search=*`.

   ```Input
   search=*
   ```

   **Resultados**
   
   ![Exemplo de consulta vazia](./media/search-explorer/search-explorer-example-empty.png "Exemplo de consulta vazia ou não qualificada")

### <a name="example-2---free-text-search"></a>Exemplo 2 – pesquisa de texto livre

Consultas de forma livre, com ou sem operadores, são úteis para simular consultas definidas pelo usuário enviadas de um aplicativo personalizado para o Azure Search. Observe que, quando você fornece termos ou expressões de consulta, a classificação de pesquisa entra em cena. O exemplo a seguir ilustra uma pesquisa de texto livre.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Você pode usar Ctrl-F para pesquisar termos específicos de interesse nos resultados.

   ![Exemplo de consulta de texto livre](./media/search-explorer/search-explorer-example-freetext.png "Exemplo de consulta de texto livre")

### <a name="example-3---count-of-matching-documents"></a>Exemplo 3 – contagem de documentos correspondentes 

Adicione **$count** para obter o número de correspondências encontradas em um índice. Em uma pesquisa vazia, a contagem é o número total de documentos no índice. Em uma pesquisa qualificada, é o número de documentos que correspondem à entrada da consulta.

   ```Input1
   $count=true
   ```
   **Resultados**

   ![Exemplo de contagem de documentos](./media/search-explorer/search-explorer-example-count.png "Contagem de documentos correspondentes no índice")

### <a name="example-4---restrict-fields-in-search-results"></a>Exemplo 4 – restringir os campos nos resultados da pesquisa

Adicione **$select** para limitar os resultados aos campos nomeados explicitamente para a saída mais legível no **Gerenciador de pesquisa**. Para manter a cadeia de caracteres de pesquisa e **$count=true**, coloque **&** como prefixo dos argumentos. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   ![Exemplo de restrição de campos](./media/search-explorer/search-explorer-example-selectfield.png "Restringir campos nos resultados da pesquisa")

### <a name="example-5---return-next-batch-of-results"></a>Exemplo 5 – retornar o próximo lote de resultados

O Azure Search retorna as primeiras 50 correspondências com base na classificação da pesquisa. Para obter o próximo conjunto de documentos correspondentes, acrescente **$top=100,&$skip=50** para aumentar o conjunto de resultados para 100 documentos (o padrão é 50, o máximo é 1000), ignorando os primeiros 50 documentos. Lembre-se de que você precisa fornecer critérios de pesquisa, como um termo ou expressão de consulta, para obter os resultados classificados. Observe que as pontuações de pesquisa diminuem quanto mais você avança nos resultados da pesquisa.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   ![Resultados da pesquisa em lote](./media/search-explorer/search-explorer-example-topskip.png "Retornar próximo lote de resultados da pesquisa")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expressões de filtro (maior que, menor que, igual a)

Use o parâmetro **$filter** quando desejar especificar critérios precisos em vez de pesquisa de texto livre. Este exemplo pesquisa quartos maior que 3:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   ![Expressão de filtro](./media/search-explorer/search-explorer-example-filter.png "Filtrar pelos critérios")

## <a name="order-by-expressions"></a>Expressões orderby

Adicione **$orderby** para classificar os resultados por outro campo além da pontuação de pesquisa. Uma expressão de exemplo que você pode usar para testar isso é:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   ![Expressão orderby](./media/search-explorer/search-explorer-example-ordery.png "Alterar a ordem de classificação")

As expressões **$filter** e **$orderby** são construções do OData. Para saber mais, confira [Sintaxe de filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Como iniciar o gerenciador de pesquisa

1. No [portal do Azure](https://portal.azure.com), abra a página de serviço de pesquisa no painel ou [localize seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página de visão geral do serviço, clique em **Gerenciador de pesquisa**.

   ![Comando do Gerenciador de pesquisa no portal](./media/search-explorer/search-explorer-cmd2.png "Comando Gerenciador de pesquisa no portal")

3. Selecione o índice a ser consultado.

   ![Selecione o índice a ser consultado](./media/search-explorer/search-explorer-changeindex-se2.png "Selecione o índice")

4. Opcionalmente, defina a versão da API. Por padrão, a versão atual disponível de forma geral da API é selecionada, mas você poderá escolher uma versão prévia ou API mais antiga, se a sintaxe que deseja usar for específica da versão.

5. Após o índice e a versão da API serem selecionados, insira os termos de pesquisa ou expressões de consulta totalmente qualificadas na barra de pesquisa e clique em **Pesquisar** para executar.

   ![Insira os termos de pesquisa e clique em Pesquisar](./media/search-explorer/search-explorer-query-string-example.png "Insira os termos de pesquisa e clique em Pesquisar")

Dicas para pesquisar no **Gerenciador de pesquisa**:

+ Os resultados são retornados como documentos JSON detalhados para que você possa exibir o conteúdo e a construção do documento na totalidade. Você pode usar expressões de consulta, mostradas nos exemplos, para limitar quais campos são retornados.

+ Os documentos contêm todos os campos marcados como **Recuperáveis** no índice. Para exibir atributos de índice no portal, clique em *realestate-us-sample* na lista **Índices** na página de visão geral da pesquisa.

+ As consultas de forma livre, semelhantes ao que você pode digitar em um navegador da Web comercial, são úteis para testar uma experiência do usuário final. Por exemplo, supondo o índice de exemplo realestate interno, você poderia digitar "Apartamentos Seattle lago washington" e, em seguida, usar Ctrl-F para localizar termos nos resultados da pesquisa. 

+ As expressões de consulta e de filtro devem ser articuladas em uma sintaxe compatível com o Azure Search. O padrão é uma [sintaxe simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), mas você pode opcionalmente usar uma [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais poderosas. [Expressões de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) são uma sintaxe de OData.


## <a name="next-steps"></a>Próximas etapas

Os recursos a seguir fornecem exemplos e informações de sintaxe de consulta adicionais.

 + [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exemplos de consulta do Lucene](search-query-lucene-examples.md) 
 + [Sintaxe de expressão do filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
