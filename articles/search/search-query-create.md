---
title: Criar uma consulta
titleSuffix: Azure Cognitive Search
description: Saiba como construir uma solicitação de consulta no Pesquisa Cognitiva, quais ferramentas e APIs usar para teste e código e como decisões de consulta começam com design de índice.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 4f5cc0d5eefd5969566040e4148ca7358d348736
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951497"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Criando consultas no Azure Pesquisa Cognitiva

Se você estiver criando uma consulta pela primeira vez, este artigo descreve abordagens e métodos para configurar consultas. Ele também apresenta uma solicitação de consulta e explica como os atributos de campo e os analisadores linguísticas podem impactar os resultados da consulta.

## <a name="whats-a-query-request"></a>O que é uma solicitação de consulta?

Uma consulta é uma solicitação somente leitura em relação à coleção docs de um único índice de pesquisa. Ele especifica que um parâmetro ' Search ' contém a expressão de consulta, que consiste em termos, frases entre aspas e operadores.

Parâmetros adicionais fornecem mais definição para a consulta e a resposta. Por exemplo, os escopos ' searchFields ' consultam a execução em campos específicos, ' Select ' especifica quais campos são retornados nos resultados e ' count ' retorna o número de correspondências encontradas no índice.

O exemplo a seguir fornece uma ideia geral de uma solicitação de consulta mostrando um subconjunto dos parâmetros disponíveis. Para obter mais informações sobre composição de consulta, consulte [tipos de consulta e composições](search-query-overview.md) e [documentos de pesquisa (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>Escolher um cliente

Você precisará de uma ferramenta como portal do Azure ou postmaster, ou código que instancia um cliente de consulta usando APIs. Recomendamos as APIs portal do Azure ou REST para desenvolvimento antecipado e teste de prova de conceito.

### <a name="permissions"></a>Permissões

Qualquer operação, incluindo solicitações de consulta, funcionará em uma [chave de API de administração](search-security-api-keys.md), mas as solicitações de consulta podem, opcionalmente, usar uma chave de API de [consulta](search-security-api-keys.md#create-query-keys). As chaves de API de consulta são altamente recomendadas. Você pode criar até 50 por serviço e atribuir chaves diferentes a aplicativos diferentes.

No portal do Azure, o acesso a ferramentas, assistentes e objetos exigem a associação na função colaborador ou acima no serviço. 

### <a name="use-azure-portal-to-query-an-index"></a>Usar portal do Azure para consultar um índice

O [Search Explorer (Portal)](search-explorer.md) é uma interface de consulta no portal do Azure que executa consultas em índices no serviço de pesquisa subjacente. Internamente, o portal faz solicitações de [pesquisa de documentos](/rest/api/searchservice/search-documents) , mas não pode invocar AutoCompletar, sugestões ou pesquisa de documentos. 

Você pode selecionar qualquer índice e versão da API REST, incluindo a visualização. Uma cadeia de caracteres de consulta pode usar sintaxe simples ou completa, com suporte para todos os parâmetros de consulta (filtro, seleção, searchFields e assim por diante). No portal, ao abrir um índice, você pode trabalhar com o Search Explorer ao lado da definição de JSON de índice em guias lado a lado para facilitar o acesso a atributos de campo. Verifique quais campos são pesquisáveis, classificável, filtráveis e com aparência durante o teste de consultas.

### <a name="use-a-rest-client"></a>Usar um cliente REST

O postmaster e o Visual Studio Code (com uma extensão para o Azure Pesquisa Cognitiva) podem funcionar como um cliente de consulta. Usando qualquer ferramenta, você pode se conectar ao serviço de pesquisa e enviar solicitações [de documentos de pesquisa (REST)](/rest/api/searchservice/search-documents) . Vários tutoriais e exemplos demonstram clientes REST para consultar a indexação. 

Comece com um destes artigos para saber mais sobre cada cliente (ambos incluem instruções para consultas):

+ [Criar um índice de pesquisa usando REST e o postmaster](search-get-started-rest.md)
+ [Introdução ao Visual Studio Code e ao Azure Cognitive Search](search-get-started-vs-code.md)

Cada solicitação é autônoma, portanto, você deve fornecer o ponto de extremidade, o nome do índice e a versão da API em cada solicitação. Outras propriedades, tipo de conteúdo e chave de API são passadas no cabeçalho da solicitação. Para obter mais informações, consulte [Pesquisar documentos (REST)](/rest/api/searchservice/search-documents) para obter ajuda com solicitações de consulta formular.

### <a name="use-an-sdk"></a>Usar um SDK

Por Pesquisa Cognitiva, os SDKs do Azure implementam recursos geralmente disponíveis. Dessa forma, você pode usar qualquer um dos SDKs para consultar um índice. Todos eles fornecem um **SearchClient** que tem métodos para interagir com um índice, desde o carregamento de um índice com documentos de pesquisa até solicitações de consulta formular.

| SDK do Azure | Cliente | Exemplos |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample. java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | Pendente. |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Escolha um tipo de consulta: simples | completo

Se sua consulta for pesquisa de texto completo, um analisador de consulta será usado para processar qualquer texto passado como termos e frases de pesquisa. O Azure Pesquisa Cognitiva oferece dois analisadores de consulta. 

+ O analisador simples compreende a [sintaxe de consulta simples](query-simple-syntax.md). Este analisador foi selecionado como o padrão para sua velocidade e eficácia em consultas de texto de forma livre. A sintaxe dá suporte a operadores de pesquisa comuns (e, ou, não) para pesquisas de termo e frase, e a pesquisa de prefixo ( `*` ) (como em "Sea *" para Seattle e Seaside). Uma recomendação geral é testar o analisador simples primeiro e, em seguida, passar para o analisador completo se os requisitos do aplicativo chamarem consultas mais poderosas.

+ A [sintaxe de consulta Lucene completa](query-Lucene-syntax.md#bkmk_syntax), habilitada quando você adiciona `queryType=full` à solicitação, é baseada no [analisador do Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

A sintaxe completa e a sintaxe simples se sobrepõem à extensão que dão suporte ao mesmo prefixo e às operações booleanas, mas a sintaxe completa fornece mais operadores. Em total, há mais operadores para expressões booleanas e mais operadores para consultas avançadas, como pesquisa difusa, pesquisa de caracteres curinga, pesquisa de proximidade e expressões regulares.

## <a name="choose-query-methods"></a>Escolher métodos de consulta

A pesquisa é fundamentalmente um exercício controlado pelo usuário, em que os termos ou frases são coletados de uma caixa de pesquisa ou de eventos de clique em uma página. A tabela a seguir resume os mecanismos pelos quais você pode coletar entrada do usuário, juntamente com a experiência de pesquisa esperada.

| Entrada | Experiência |
|-------|---------|
| [Método de pesquisa](/rest/api/searchservice/search-documents) | Um usuário digita termos ou frases em uma caixa de pesquisa, com ou sem operadores, e clica em Pesquisar para enviar a solicitação. A pesquisa pode ser usada com filtros na mesma solicitação, mas não com preenchimento automático ou sugestões. |
| [Método de preenchimento automático](/rest/api/searchservice/autocomplete) | Um usuário digita alguns caracteres e as consultas são iniciadas depois que cada caractere novo é digitado. A resposta é uma cadeia de caracteres completa do índice. Se a cadeia de caracteres fornecida for válida, o usuário clicará em Pesquisar para enviar essa consulta ao serviço. |
| [Método Suggestions](/rest/api/searchservice/suggestions) | Assim como acontece com o preenchimento automático, um usuário digita alguns caracteres e as consultas incrementais são geradas. A resposta é uma lista suspensa de documentos correspondentes, normalmente representada por alguns campos exclusivos ou descritivos. Se qualquer uma das seleções for válida, o usuário clica em um e o documento correspondente é retornado. |
| [Navegação facetada](/rest/api/searchservice/search-documents#query-parameters) | Uma página mostra links de navegação clicáveis ou trilhas que restringem o escopo da pesquisa. Uma estrutura de navegação facetada é composta dinamicamente com base em uma consulta inicial. Por exemplo, `search=*` para preencher uma árvore de navegação facetada composta de todas as categorias possíveis. Uma estrutura de navegação facetada é criada a partir de uma resposta de consulta, mas também é um mecanismo para expressar a próxima consulta. n referência da API REST, `facets` está documentada como um parâmetro de consulta de uma operação de pesquisa de documentos, mas pode ser usada sem o `search` parâmetro.|
| [Método do filtro](/rest/api/searchservice/search-documents#query-parameters) | Os filtros são usados com facetas para restringir os resultados. Você também pode implementar um filtro por trás da página, por exemplo, para inicializar a página com campos específicos do idioma. Na referência da API REST, `$filter` é documentado como um parâmetro de consulta de uma operação pesquisar documentos, mas pode ser usado sem o `search` parâmetro.|

## <a name="know-your-field-attributes"></a>Conhecer seus atributos de campo

Se você revisou os [tipos de consulta e a composição](search-query-overview.md)anteriormente, talvez se lembre de que os parâmetros na solicitação de consulta dependem de como os campos são atribuídos em um índice. Por exemplo, para ser usado em uma consulta, filtro ou ordem de classificação, um campo deve ser *pesquisável*, *filtrável* e *classificável*. Da mesma forma, somente os campos marcados como *recuperáveis* podem aparecer nos resultados. Ao começar a especificar os `search` parâmetros, `filter` e `orderby` em sua solicitação, certifique-se de verificar os atributos à medida que você for para evitar resultados inesperados.

Na captura de tela do portal Abaixo do [índice de exemplo de hotéis](search-get-started-portal.md), somente os dois últimos campos "LastRenovationDate" e "rating" podem ser usados em uma `"$orderby"` única cláusula.

![Definição de índice para o exemplo de Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para o exemplo de Hotel")

Para obter uma descrição dos atributos de campo, consulte [criar índice (API REST)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Conhecer seus tokens

Durante a indexação, o mecanismo de pesquisa usa um analisador para executar a análise de texto em cadeias de caracteres, maximizando o potencial para correspondência no momento da consulta. No mínimo, as cadeias de caracteres estão em letras minúsculas, mas também podem passar por lematização e parar a remoção do Word. Cadeias de caracteres maiores ou palavras compostas normalmente são divididas por espaço em branco, hifens ou traços e indexadas como tokens separados. 

O ponto a ser levado aqui é que o que você considera que o índice contém, e o que realmente está nele, pode ser diferente. Se as consultas não retornarem os resultados esperados, você poderá inspecionar os tokens criados pelo analisador por meio do [analisar texto (API REST)](/rest/api/searchservice/test-analyzer). Para obter mais informações sobre a geração de tokens e o impacto sobre as consultas, consulte [pesquisa de termo parcial e padrões com caracteres especiais](search-query-partial-matching.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma melhor compreensão de como as solicitações de consulta funcionam, experimente os guias de início rápido a seguir para ter experiência prática.

+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em REST](search-get-started-rest.md)
+ [Como consultar em .NET](search-get-started-dotnet.md)
+ [Como consultar em Python](search-get-started-python.md)
+ [Como consultar em JavaScript](search-get-started-javascript.md)