---
title: Crie um índice
titleSuffix: Azure Cognitive Search
description: Apresenta conceitos e ferramentas de indexação no Pesquisa Cognitiva do Azure, incluindo definições de esquema e a estrutura de dados física.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d0cc7630a3bea67a99c3cb65d2015e934e8ac2da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539087"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Criando índices de pesquisa no Azure Pesquisa Cognitiva

Pesquisa Cognitiva armazena o conteúdo pesquisável usado para texto completo e consultas filtradas em um *índice de pesquisa*. Um índice é definido por um esquema e salvo no serviço, com a importação de dados a seguir como uma segunda etapa. 

Os índices contêm *documentos de pesquisa*. Conceitualmente, um documento é uma única unidade de dados pesquisáveis no índice. Um varejista pode ter um documento para cada produto, uma organização de notícias pode ter um documento para cada artigo e assim por diante. Mapeando esses conceitos para equivalentes de banco de dados mais conhecidos: um *índice de pesquisa* é igual a uma *tabela* e os *documentos* são aproximadamente equivalentes a *linhas* em uma tabela.

## <a name="whats-an-index-schema"></a>O que é um esquema de índice?

A estrutura física de um índice é determinada pelo esquema. A coleção ' Fields ' normalmente é a parte maior de um índice, em que cada campo é nomeado, atribuído a um [tipo de dados](/rest/api/searchservice/Supported-data-types)e com comportamentos permitidos que determinam como ele é usado.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Outros elementos são recolhidos para fins de brevidade, mas os links a seguir podem fornecer os detalhes: [sugestores](index-add-suggesters.md), [perfis de Pontuação](index-add-scoring-profiles.md), [analisadores](search-analyzers.md) usados para processar cadeias de caracteres em tokens de acordo com as regras linguísticas ou outras características com suporte do analisador e configurações de [script remoto entre origens (CORS)](#corsoptions) .

## <a name="choose-a-client"></a>Escolher um cliente

Há várias metodologias para criar um índice de pesquisa. Recomendamos as APIs portal do Azure ou REST para desenvolvimento antecipado e teste de prova de conceito.

Durante o desenvolvimento, planeje recompilações frequentes. Como as estruturas físicas são criadas no serviço, é necessário [descartar e recriar índices](search-howto-reindex.md) para a maioria das modificações em uma definição de campo existente. Considere trabalhar com um subconjunto de seus dados para acelerar as recompilações.

### <a name="permissions"></a>Permissões

Todas as operações relacionadas a um índice de pesquisa, incluindo GET solicita sua definição, exigem uma [chave de API de administração](search-security-api-keys.md) na solicitação.

### <a name="limits"></a>Limites

Todas as [camadas de serviço limitam](search-limits-quotas-capacity.md#index-limits) o número de objetos que você pode criar. Se você estiver experimentando a camada gratuita, só poderá ter 3 índices em um determinado momento.

### <a name="use-azure-portal-to-create-a-search-index"></a>Usar portal do Azure para criar um índice de pesquisa

O portal fornece duas opções para criar um índice de pesquisa: [**Assistente de importação de dados**](search-import-data-portal.md) e **Adicionar índice** que fornece campos para especificar um esquema de índice. Os pacotes do assistente em operações adicionais também criam um indexador, uma fonte de dados e dados de carregamento. Se isso for maior do que o desejado, você deve usar apenas **Adicionar índice** ou outra abordagem.

A captura de tela a seguir mostra onde você pode encontrar **Adicionar índice** no Portal. **Importar dados** é o próximo momento da próxima porta.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Comando adicionar índice" border="true":::

> [!Tip]
> O design de índice por meio do portal impõe requisitos e regras de esquema para tipos de dados específicos, como a despermissão de recursos de pesquisa de texto completo em campos numéricos. Depois de ter um índice funcional, você pode copiar o JSON do portal e adicioná-lo à sua solução.

### <a name="use-a-rest-client"></a>Usar um cliente REST

O postmaster e o Visual Studio Code (com uma extensão para o Azure Pesquisa Cognitiva) podem funcionar como um cliente de índice de pesquisa. Usando qualquer ferramenta, você pode se conectar ao serviço de pesquisa e enviar solicitações [de criar índice (REST)](/rest/api/searchservice/create-index) . Há vários tutoriais e exemplos que demonstram clientes REST para a criação de objetos. 

Comece com um destes artigos para saber mais sobre cada cliente:

+ [Criar um índice de pesquisa usando REST e o postmaster](search-get-started-rest.md)
+ [Introdução ao Visual Studio Code e ao Azure Cognitive Search](search-get-started-vs-code.md)

Consulte as [operações de índice (REST)](/rest/api/searchservice/index-operations) para obter ajuda com solicitações de índice formular.

### <a name="use-an-sdk"></a>Usar um SDK

Por Pesquisa Cognitiva, os SDKs do Azure implementam recursos geralmente disponíveis. Dessa forma, você pode usar qualquer um dos SDKs para criar um índice de pesquisa. Todos eles fornecem um **SearchIndexClient** que tem métodos para criar e atualizar índices.

| SDK do Azure | Cliente | Exemplos |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [Azure-Search-dotnet-Samples/QuickStart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Índices](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>Definir campos

Um documento de pesquisa é definido pela `fields` coleção. Você precisará de campos para consultas e chaves. Você provavelmente também precisará de campos para dar suporte a filtros, facetas e classificações. Você também pode precisar de campos para dados que um usuário nunca vê, por exemplo, você pode desejar campos para margens de lucro ou promoções de marketing que você pode usar para modificar a classificação de pesquisa.

Um campo do tipo EDM. String deve ser designado como a chave do documento. Ele é usado para identificar exclusivamente cada documento de pesquisa. Você pode recuperar um documento por sua chave para preencher uma página de detalhes.  

Se os dados de entrada forem hierárquicos por natureza, atribua o tipo de dados de [tipo complexo](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de exemplo interno, hotéis, ilustra os tipos complexos usando um endereço (contém vários subcampos) que têm uma relação um-para-um com cada hotel e uma coleção complexa de salas, em que várias salas são associadas a cada hotel. 

Atribua qualquer analisador a campos de cadeia de caracteres antes de criar o índice. Faça o mesmo para sugestores se desejar habilitar o preenchimento automático em campos específicos.

<a name="index-attributes"></a>

### <a name="attributes"></a>Atributos

Os atributos de campo determinam como um campo é usado, por exemplo, se ele é usado na pesquisa de texto completo, na navegação facetada, nas operações de classificação e assim por diante. 

Campos de cadeia de caracteres geralmente são marcados como "pesquisáveis" e "recuperáveis". Os campos usados para restringir os resultados da pesquisa incluem "classificável", "filtrável" e "facetable".

|Atributo|Descrição|  
|---------------|-----------------|  
|pesquisável |Pesquisável com texto completo, sujeito à análise lexical como separação de palavras durante a indexação. Se você definir um campo pesquisável com um valor como “dia ensolarado”, internamente, ele será dividido nos tokens individuais “dia” e “ensolarado”. Para obter detalhes, consulte [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md).|  
|filtrável |Referenciado nas consultas $filter. Campos filtráveis dos tipos `Edm.String` ou `Collection(Edm.String)` não são submetidos à separação de palavras. Portanto, as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo f como “dia ensolarado”, `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` as encontrará. |  
|classificável |Por padrão, o sistema classifica os resultados pela pontuação, mas você pode configurar a classificação com base nos campos dos documentos. Os campos do tipo `Collection(Edm.String)` não podem ser "classificável". |  
|com faceta |Normalmente, usado em uma apresentação dos resultados da pesquisa que inclui uma contagem de ocorrências por categoria (por exemplo, hotéis em uma cidade específica). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Campos de tipo `Edm.String` que são filtráveis, "classificável" ou "facetable" podem ter, no máximo, 32 quilobytes de comprimento. Para obter detalhes, consulte [Criar índice (API REST)](/rest/api/searchservice/create-index).|  
|chaves |Identificador exclusivo para documentos no índice. Exatamente um campo deve ser escolhido como o campo chave e deve ser do tipo `Edm.String`.|  
|recuperáveis |Determina se o campo pode ser retornado em um resultado da pesquisa. Isso é útil quando você deseja usar um campo (por exemplo, *margem de lucro*) como mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo seja visível para o usuário final. Esse atributo deve ser `true` for `key` .|  

Embora seja possível adicionar novos campos a qualquer momento, as definições de campo existentes são bloqueadas durante o tempo de vida do índice. Por esse motivo, os desenvolvedores geralmente usam o portal para criar índices simples, testar ideias ou usar as páginas do portal para pesquisar uma configuração. A iteração frequente em um design de índice será mais eficiente se você seguir uma abordagem baseada em código, de modo que você possa recriar o índice com facilidade.

> [!NOTE]
> As APIs usadas para criar um índice têm comportamentos padrão variados. Para as [APIs REST](/rest/api/searchservice/Create-Index), a maioria dos atributos é habilitada por padrão (por exemplo, "pesquisável" e "recuperável" são verdadeiros para campos de cadeia de caracteres) e, muitas vezes, você só precisa defini-los se desejar desativá-los. Para o SDK do .NET, o oposto é true. Em qualquer propriedade que você não definir explicitamente, o padrão é desabilitar o comportamento de pesquisa correspondente, a menos que você o habilite especificamente.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atributos e tamanho do índice (implicações de armazenamento)

O tamanho de um índice é determinado pelo tamanho dos documentos que você carrega, além da configuração de índice, como se você inclui sugestores e como você define atributos em campos individuais. 

A captura de tela a seguir ilustra padrões de armazenamento de índice resultantes de várias combinações de atributos. O índice é baseado no **índice de exemplo de imóveis**, que você pode criar facilmente usando o assistente de importação de dados. Embora os esquemas de índice não sejam mostrados, é possível inferir os atributos com base no nome do índice. Por exemplo, o índice *realestate-pesquisável* tem o atributo "pesquisável" selecionado e nada mais, o índice *realestate-recuperável* tem o atributo "recuperável" selecionado e nada mais, e assim por diante.

![Tamanho do índice com base na seleção de atributo](./media/search-what-is-an-index/realestate-index-size.png "Tamanho do índice com base na seleção de atributo")

Embora essas variantes de índice sejam artificiais, podemos referenciá-las para obter amplas comparações de como os atributos afetam o armazenamento. Definir "recuperável" aumenta o tamanho do índice? Não. Adicionar campos a um **Sugestor** aumenta o tamanho do índice? Sim. 

Tornar um campo filtrável ou classificável também adiciona ao consumo de armazenamento porque os campos filtrados e classificados não são indexados para que as sequências de caracteres possam corresponder textualmente.

Também não refletida na tabela acima é o impacto dos [analisadores](search-analyzers.md). Se você estiver usando o edgeNgram criador para armazenar sequências idênticas de caracteres (a, AB, ABC, abcd), o tamanho do índice será maior do que se você tiver usado um analisador padrão.

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação do Azure Pesquisa Cognitiva e pode ser alterada sem aviso prévio. Não há nenhuma garantia de que o comportamento atual persistirá no futuro.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Sobre o `corsOptions`

Os esquemas de índice incluem uma seção para configuração `corsOptions` . O JavaScript do lado do cliente não pode chamar nenhuma API por padrão, pois o navegador impedirá todas as solicitações entre origens. Para permitir ao índice as consultas entre origens, habilite o CORS (Compartilhamento de Recursos entre Origens) definindo o atributo **corsOptions**. Por motivos de segurança, apenas APIs de consulta dão suporte para CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): esta é uma lista de origens que terão acesso concedido ao índice. Isso significa que qualquer código JavaScript atendendo a partir dessas origens terá permissão para consultar o índice (supondo que ele forneça a chave de API correta). Cada origem é tipicamente da forma, `protocol://<fully-qualified-domain-name>:<port>` embora `<port>` seja frequentemente omitida. Consulte [Compartilhamento de recursos entre origens (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se você quiser permitir acesso a todas as origens, inclua `*` como um único item na matriz **allowedOrigins**. *Essa não é uma prática recomendada para serviços de pesquisa de produção*, mas geralmente é útil para desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): os navegadores usam esse valor para determinar a duração (em segundos) para respostas de simulação de CORS de cache. Esse deve ser um inteiro não negativo. Quanto maior for esse valor, melhor será o desempenho, porém, mais tempo levará para que as alterações de política CORS entrem em vigor. Se ele não for definido, uma duração padrão de cinco minutos será usada.

## <a name="next-steps"></a>Próximas etapas

Você pode obter experiência prática ao criar um índice usando quase qualquer exemplo ou passo a passos para Pesquisa Cognitiva. Para iniciantes, você pode escolher qualquer um dos guias de início rápido do Sumário.

Mas você também desejará familiarizar-se com metodologias para carregar um índice com dados. As estratégias de definição de índice e de importação de dados são definidas em tandem. Os artigos a seguir fornecem mais informações sobre como carregar um índice.

+ [Visão geral da importação de dados](search-what-is-data-import.md)

+ [Adicionar, atualizar ou excluir documentos (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 