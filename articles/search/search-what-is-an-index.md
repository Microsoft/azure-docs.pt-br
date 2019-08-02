---
title: Criar uma definição de índice e conceitos - Azure Search
description: Introdução para indexar termos e conceitos no Azure Search, incluindo partes de componentes e estrutura física.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 0a6a5b0e3957141b9ea17a378a7cbeff33a0124e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485195"
---
# <a name="create-a-basic-index-in-azure-search"></a>Criar um índice básico no Azure Search

No Azure Search, um *índice* é o repositório persistente de *documentos* e outras construções usados para pesquisa filtrada e de texto completo em um serviço do Azure Search. Conceitualmente, um documento é uma única unidade de dados pesquisáveis no índice. Por exemplo, uma loja de comércio eletrônico pode ter um documento para cada item vendido, uma agência de notícias pode ter um documento para cada artigo e assim por diante. Mapeando esses conceitos para equivalentes de banco de dados mais conhecidos: um *índice* é conceitualmente semelhante a uma *tabela* e *documentos* são equivalentes a *linhas* em uma tabela.

Ao adicionar ou carregar um índice, o Azure Search criará estruturas físicas com base no esquema que você fornecer. Por exemplo, se um campo no índice estiver marcado como pesquisável, um índice invertido será criado para esse campo. Posteriormente, ao adicionar ou carregar documentos ou enviar consultas de pesquisa para o Azure Search, você estará enviando solicitações para um índice específico no serviço de pesquisa. Carregar campos com valores de documento é chamado de *indexação* ou ingestão de dados.

É possível criar um índice no portal, na [API REST](search-create-index-rest-api.md) ou no [SDK do .NET](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Fluxo de trabalho recomendado

Normalmente, chegar no design de índice correto é obtido por meio de várias iterações. O uso de uma combinação de ferramentas e APIs pode ajudar você a finalizar seu design rapidamente.

1. Determine se você pode usar um [indexador](search-indexer-overview.md#supported-data-sources). Se os dados externos forem uma das fontes de dados com suporte, será possível criar um protótipo e carregar um índice usando o assistente [**Importar dados**](search-import-data-portal.md).

2. Se você não puder usar **Importar dados**, ainda será possível [criar um índice inicial no portal](search-create-index-portal.md), adicionando campos, tipos de dados e atribuindo atributos usando controles na página **Adicionar índice**. O portal mostra quais atributos estão disponíveis para diferentes tipos de dados. Se você for novo no design de índice, isso será útil.

   ![Adicionar página de índice mostrando atributos por tipo de dados](media/search-create-index-portal/field-attributes.png "Adicionar página de índice mostrando atributos por tipo de dados")
  
   Quando você clica em **Criar**, todas as estruturas físicas que dão suporte ao seu índice são criadas em seu serviço de pesquisa.

3. Baixe o esquema de índice usando a [API REST Obter índice](https://docs.microsoft.com/rest/api/searchservice/get-index) e uma ferramenta de teste da Web como [Postman](search-get-started-postman.md). Agora você tem uma representação JSON do índice criado no portal. 

   Você está mudando para uma abordagem baseada em código neste momento. O portal não é adequado para iteração, porque não é possível editar um índice já criado. Mas é possível usar o Postman e REST para as tarefas restantes.

4. [Carregue seu índice com os dados](search-what-is-data-import.md). O Azure Search aceita documentos JSON. Para carregar seus dados de maneira programática, é possível usar o Postman com documentos JSON no conteúdo da solicitação. Se os dados não estiverem expressados facilmente como JSON, essa etapa será a mais trabalhosa.

5. Consulte seu índice, examine resultados e itere o esquema de índice até que você comece a ver os resultados esperados. É possível usar o [**Gerenciador de pesquisa**](search-explorer.md) ou o Postman para consultar seu índice.

6. Continue usando o código para iterar em seu design.  

Como estruturas físicas são criadas no serviço do [descartar e recriar índices](search-howto-reindex.md) é necessário sempre que você fizer alterações de material para uma definição de campo existente. Isso significa que, durante o desenvolvimento, você deve planejar recompilações frequentes. Considere trabalhar com um subconjunto de seus dados para acelerar as recompilações. 

O código, em vez de uma abordagem de portal, é recomendado para design iterativo. Se você depender do portal para a definição de índice, terá que preencher a definição de índice em cada recompilação. Como alternativa, ferramentas como [o Postman e a API REST](search-get-started-postman.md) são úteis para testes de prova de conceito quando os projetos de desenvolvimento ainda estão em fases iniciais. É possível fazer alterações incrementais em uma definição de índice em um corpo da solicitação e, em seguida, enviar a solicitação para seu serviço a fim de recriar um índice usando um esquema atualizado.

## <a name="components-of-an-index"></a>Componentes de um índice

Esquematicamente, um índice do Azure Search é composto dos seguintes elementos. 

A [*coleção de campos*](#fields-collection) normalmente é a maior parte de um índice, onde cada campo é nomeado, digitado e atribuído com comportamentos permitidos que determinam como é usado. Outros elementos incluem [sugestores](#suggesters), [perfis de pontuação](#scoring-profiles), [analisadores](#analyzers) com partes de componentes para dar suporte à personalização, [CORS](#cors) e [chave de criptografia](#encryption-key) opções.

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
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
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Coleção e atributo de campos

Quando você define o esquema, deve especificar o nome, tipo e atributos de cada campo no índice. O tipo de campo classifica os dados armazenados nesse campo. Os atributos são definidos em campos individuais para especificar como o campo será usado. A tabela a seguir enumera os tipos e atributos que você pode especificar.

### <a name="data-types"></a>Tipos de dados
| Type | DESCRIÇÃO |
| --- | --- |
| *Edm.String* |O texto que opcionalmente pode ser indexado para a pesquisa de texto completo (separação de palavras, derivação e assim por diante). |
| *Collection(Edm.String)* |Uma lista de cadeias de caracteres que opcionalmente podem ser indexadas para a pesquisa de texto completo. Não há nenhum limite teórico superior no número de itens em uma coleção, mas o limite superior de 16 MB no tamanho da carga se aplica às coleções. |
| *Edm.Boolean* |Contém valores true/false. |
| *Edm.Int32* |Valores inteiros de 32 bits. |
| *Edm.Int64* |Valores inteiros de 64 bits. |
| *Edm.Double* |Dados numéricos de dupla precisão. |
| *Edm.DateTimeOffset* |Valores de data e hora representados no formato OData V4 (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Um ponto que representa uma localização geográfica em todo o mundo. |

Você pode encontrar informações mais detalhadas sobre os [tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) do Azure Search.

### <a name="index-attributes"></a>Atributos de índice

Exatamente um campo no índice deve ser designado como um **chave** campo que identifica exclusivamente cada documento.

Outros atributos determinam como um campo é usado em um aplicativo. Por exemplo, o **pesquisável** atributo é atribuído a todos os campos que devem ser incluídos em uma pesquisa de texto completo. 

As APIs usadas para criar um índice tem comportamentos diferentes do padrão. Para o [APIs REST](https://docs.microsoft.com/rest/api/searchservice/Create-Index), a maioria dos atributos estão habilitados por padrão (por exemplo, **pesquisáveis** e **recuperáveis** são verdadeiros para campos de cadeia de caracteres) e você geralmente só precisará defini-las se convém desativá-los. Para o SDK do .NET, o oposto é verdadeiro. Em qualquer propriedade que você não definir explicitamente, o padrão é desabilitar o comportamento de pesquisa correspondente, a menos que você o habilite especificamente.

| Atributo | DESCRIÇÃO |
| --- | --- |
| `key` |Uma cadeia de caracteres que fornece a ID exclusiva de cada documento, usada para pesquisar documentos. Todos os índices devem ter uma chave. Somente um campo pode ser a chave e seu tipo deve ser definido para Edm.String. |
| `retrievable` |Especifica se um campo pode ser retornado em um resultado da pesquisa. |
| `filterable` |Permite que o campo seja usado nas consultas de filtro. |
| `Sortable` |Permite que uma consulta classifique os resultados da pesquisa usando esse campo. |
| `facetable` |Permite que um campo seja usado em uma estrutura de [navegação mista](search-faceted-navigation.md) para a filtragem autodirecionada do usuário. Normalmente, os campos que contêm valores repetidos que você pode usar para agrupar vários documentos (por exemplo, vários documentos que estejam em uma única categoria de marca ou de serviço) funcionam melhor como facetas. |
| `searchable` |Marca o campo como texto completo pesquisável. |


## <a name="storage-implications"></a>Implicações de armazenamento

Os atributos selecionados têm um impacto no armazenamento. A captura de tela a seguir ilustra padrões de armazenamento de índice resultantes de várias combinações de atributos.

O índice se baseia a [exemplo interno de imóveis](search-get-started-portal.md) fonte de dados, você pode indexar e consultar no portal. Embora os esquemas de índice não sejam mostrados, é possível inferir os atributos com base no nome do índice. Por exemplo, o índice *realestate-searchable* tem o atributo **searchable** selecionado e nada mais, o índice *realestate-retrievable* tem o atributo **retrievable** selecionado e nada mais e assim por diante.

![Indexar tamanho com base na seleção de atributo](./media/search-what-is-an-index/realestate-index-size.png "Indexar tamanho com base na seleção de atributo")

Embora essas variantes de índice sejam artificiais, podemos referenciá-las para obter amplas comparações de como os atributos afetam o armazenamento. A configuração **recuperável** aumenta o tamanho do índice? Não. A adição de campos a um **Sugestor** aumenta o tamanho do índice? Sim.

Os índices que dão suporte à filtragem e à classificação são proporcionalmente maiores do que os índices que dão suporte apenas à pesquisa de texto completo. O motivo é que isso filtra e classifica consultas em correspondências exatas, assim os documentos são armazenados intactos. Por outro lado, os campos pesquisáveis que dão suporte à pesquisa de texto completo e difusa usam índices invertidos, populados com termos indexados que consomem menos espaço do que documentos inteiros.

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação do Azure Search e poderia alterada sem aviso prévio. Não há nenhuma garantia de que o comportamento atual persistirá no futuro.

## <a name="suggesters"></a>Sugestores
Um sugestor é uma seção do esquema que define quais campos em um índice são usados para dar suporte a consultas de preenchimento automático ou digitação antecipada em pesquisas. Normalmente, cadeias de caracteres de pesquisa parciais são enviadas às [Sugestões (API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions) enquanto o usuário está digitando uma consulta de pesquisa e a API retorna um conjunto de frases sugeridas. 

Os campos adicionados a um sugestor são usados para criar termos de pesquisa de digitação antecipada. Todos os termos de pesquisa são criados durante a indexação e armazenados separadamente. Para saber mais sobre como criar uma estrutura de sugestor, confira [Add suggesters](index-add-suggesters.md) (Adicionar sugestores).

## <a name="scoring-profiles"></a>Perfis de pontuação

Um [perfil de pontuação](index-add-scoring-profiles.md) é uma seção do esquema que define comportamentos de pontuação personalizados que permitem influenciar quais itens são exibidos em uma posição mais alta nos resultados da pesquisa. Perfis de pontuação são compostos de funções e pesos de campos. Para usá-las, você pode especificar um perfil por nome na sequência de consulta.

Um padrão de pontuação perfil funciona nos bastidores para calcular uma pontuação para cada item em um conjunto de resultados de pesquisa. Você pode usar o perfil de pontuação interno e sem o nome. Como alternativa, defina **defaultScoringProfile** para usar um perfil personalizado como o padrão, invocado sempre que um perfil personalizado não é especificado na cadeia de caracteres de consulta.

## <a name="analyzers"></a>Analisadores

O elemento analisadores define o nome do analisador de linguagem a ser usado para o campo. Para saber mais sobre o intervalo de analisadores disponíveis para você, confira [Adding analyzers to an Azure Search index](search-analyzers.md) (Adicionando analisadores ao índice do Azure Search). Os analisadores só podem ser usados com campos pesquisáveis. Depois que o analisador é atribuído a um campo, ele não pode ser alterado, a menos que você recompile o índice.

## <a name="cors"></a>CORS

O JavaScript do lado do cliente não pode chamar nenhuma API por padrão, pois o navegador impedirá todas as solicitações entre origens. Para permitir ao índice as consultas entre origens, habilite o CORS (Compartilhamento de Recursos entre Origens) definindo o atributo **corsOptions**. Por motivos de segurança, apenas APIs de consulta dão suporte para CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): Essa é uma lista de origens que receberão acesso ao índice. Isso significa que qualquer código JavaScript atendendo a partir dessas origens terá permissão para consultar o índice (supondo que ele forneça a chave de API correta). Cada origem é tipicamente da forma, `protocol://<fully-qualified-domain-name>:<port>` embora `<port>` seja frequentemente omitida. Consulte [Compartilhamento de recursos entre origens (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se você quiser permitir acesso a todas as origens, inclua `*` como um único item na matriz **allowedOrigins**. *Essa não é uma prática recomendada para serviços de pesquisa de produção*, mas geralmente é útil para desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): Os navegadores usam esse valor para determinar a duração (em segundos) para armazenar em cache as respostas CORS de simulação. Esse deve ser um inteiro não negativo. Quanto maior for esse valor, melhor será o desempenho, porém, mais tempo levará para que as alterações de política CORS entrem em vigor. Se ele não for definido, uma duração padrão de cinco minutos será usada.

## <a name="encryption-key"></a>Chave de criptografia

Enquanto todos os índices de pesquisa do Azure são criptografados por padrão usando chaves gerenciadas pela Microsoft, os índices podem ser configurados para serem criptografados com **chaves gerenciadas do cliente** no cofre de chaves. Para obter mais informações, consulte [gerenciar chaves de criptografia no Azure Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Próximas etapas

Com o conhecimento adquirido sobre a composição de índice, você poderá continuar no portal para criar seu primeiro índice.

> [!div class="nextstepaction"]
> [Adicionar um índice (portal)](search-create-index-portal.md)
