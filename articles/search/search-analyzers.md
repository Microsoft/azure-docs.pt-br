---
title: Analisadores para processamento linguístico e de texto
titleSuffix: Azure Cognitive Search
description: Atribua analisadores a campos de pesquisa de texto pesquisáveis em um índice para substituir Lucene Standard padrão com alternativas personalizadas específicas a um idioma ou predefinidas.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bbda4268ca00d1c12f851517e2b35add7fba7f9b
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694293"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analisadores para processamento de texto no Azure Pesquisa Cognitiva

Um *analisador* é um componente do [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cadeias de caracteres de consulta e documentos indexados. O processamento de texto (também conhecido como análise léxica) é transformação, modificando uma cadeia de caracteres por meio de ações como estas:

+ Remover palavras não essenciais (palavras irrelevantes) e Pontuação
+ Dividir frases e hifenizar palavras em partes do componente
+ Letras minúsculas de qualquer palavra em letras maiúsculas
+ Reduza as palavras em formulários raiz primitivos para eficiência de armazenamento e para que as correspondências possam ser encontradas independentemente do conjugação

A análise se aplica a `Edm.String` campos marcados como "pesquisáveis", que indica a pesquisa de texto completo. Para campos com essa configuração, a análise ocorre durante a indexação quando os tokens são criados e, novamente durante a execução da consulta, quando as consultas são analisadas e o mecanismo verifica se há tokens correspondentes. Uma correspondência é mais provável de ocorrer quando o mesmo analisador é usado para indexação e consultas, mas você pode definir o analisador para cada carga de trabalho de forma independente, dependendo dos seus requisitos.

Os tipos de consulta que não são pesquisa de texto completo, como expressão regular ou pesquisa difusa, não passam pela fase de análise no lado da consulta. Em vez disso, o analisador envia essas cadeias de caracteres diretamente para o mecanismo de pesquisa, usando o padrão que você fornece como base para a correspondência. Normalmente, esses formulários de consulta exigem tokens de cadeia de caracteres inteiras para fazer o trabalho de correspondência de padrões. Para obter os símbolos de termos inteiros durante a indexação, talvez você precise de [analisadores personalizados](index-add-custom-analyzers.md). Para obter mais informações sobre quando e por que os termos de consulta são analisados, consulte [pesquisa de texto completo no Azure pesquisa cognitiva](search-lucene-query-architecture.md).

Para obter mais informações sobre análise lexical, ouça o seguinte clipe de vídeo para obter uma breve explicação.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Analisador padrão  

No Azure Pesquisa Cognitiva consultas, um analisador é invocado automaticamente em todos os campos de cadeia de caracteres marcados como pesquisáveis. 

Por padrão, o Azure Pesquisa Cognitiva usa o [analisador padrão do Apache Lucene (Lucene padrão)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), que quebra o texto em elementos seguindo as regras de ["segmentação de texto Unicode"](https://unicode.org/reports/tr29/) . Além disso, o analisador padrão converte todos os caracteres em sua forma em letras minúsculas. Documentos indexados e termos de pesquisa são submetidos a análise durante a indexação e o processamento de consultas.  

Você pode substituir o padrão campo por campo. Analisadores alternativos podem ser um [analisador de linguagem](index-add-language-analyzers.md) para processamento linguístico, um [analisador personalizado](index-add-custom-analyzers.md)ou um analisador predefinido da [lista de analisadores disponíveis](index-add-custom-analyzers.md#AnalyzerTable).

## <a name="types-of-analyzers"></a>Tipos de analisadores

A lista a seguir descreve quais analisadores estão disponíveis no Azure Pesquisa Cognitiva.

| Categoria | Descrição |
|----------|-------------|
| [Analisador Lucene padrão](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Padrão. Nenhuma especificação ou a configuração é necessária. Esse analisador de uso geral tem um bom desempenho para muitos idiomas e cenários.|
| Analisadores predefinidos | Oferecidos como um produto acabado destinado a ser usado no estado em que se encontra. <br/>Há dois tipos: especializado e de idioma. O que os torna "predefinidos" é que você os referencia por nome, sem nenhuma configuração ou personalização. <br/><br/>[Analisadores especializados (independentes de idioma)](index-add-custom-analyzers.md#AnalyzerTable) são usados quando as entradas de texto exigem processamento especializado ou o mínimo de processamento. Analisadores de idioma não predefinidos incluem **Asciifolding**, **Palavra-chave**, **Padrão**, **Simples**, **Interromper**, **Espaço em branco**.<br/><br/>[Analisadores de idioma](index-add-language-analyzers.md) são usados quando você precisa de suporte linguístico avançado para idiomas individuais. O Azure Pesquisa Cognitiva dá suporte a analisadores de linguagem 35 Lucene e a 50 analisadores de processamento de idioma natural da Microsoft. |
|[Analisadores personalizados](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refere-se a uma configuração definida por usuário de uma combinação de elementos existentes, consistindo em um tokenizer (obrigatório) e filtros opcionais (char ou token).|

Alguns analisadores predefinidos, como **Padrão** ou de **Parar**, dão suporte a um conjunto limitado de opções de configuração. Para definir essas opções, você efetivamente cria um analisador personalizado, consistindo de um analisador predefinido e uma das opções alternativas documentadas na [Referência do analisador predefinido](index-add-custom-analyzers.md#AnalyzerTable). Como ocorre com qualquer configuração personalizada, forneça sua nova configuração com um nome, como *myPatternAnalyzer* para distingui-lo do analisador de padrões Lucene.

## <a name="how-to-specify-analyzers"></a>Como especificar os analisadores

A definição de um analisador é opcional. Como regra geral, tente usar o analisador Lucene padrão primeiro para ver como ele é executado. Se as consultas falharem ao retornar os resultados esperados, a alternância para um analisador diferente geralmente é a solução certa.

1. Ao criar uma definição de campo no [índice](/rest/api/searchservice/create-index), defina a propriedade do  **analisador** como um dos seguintes: um [analisador predefinido](index-add-custom-analyzers.md#AnalyzerTable) , como `keyword` um [analisador de idioma](index-add-language-analyzers.md) , como `en.microsoft` , ou um analisador personalizado (definido no mesmo esquema de índice).  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   Se você estiver usando um [analisador de linguagem](index-add-language-analyzers.md), deverá usar a propriedade do **analisador** para especificá-lo. As propriedades **searchAnalyzer** e **indexAnalyzer** não dão suporte a analisadores de idioma.

1. Como alternativa, defina **indexAnalyzer** e **searchAnalyzer** para variar o analisador para cada carga de trabalho. Essas propriedades são definidas juntas e substituem a propriedade do **analisador** , que deve ser NULL. Você pode usar analisadores diferentes para preparação e recuperação de dados se uma dessas atividades exigir uma transformação específica não necessária para a outra.

   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "whitespace"
    },
   ```

1. Somente para os analisadores personalizados, crie uma entrada na seção **[analisadores]** do índice e atribua seu analisador personalizado à definição de campo por qualquer uma das duas etapas anteriores. Para saber mais, confira [Criar Índice](/rest/api/searchservice/create-index) e também [Adicionar analisadores personalizados](index-add-custom-analyzers.md).

## <a name="when-to-add-analyzers"></a>Quando adicionar analisadores

O melhor momento para adicionar e atribuir analisadores é durante o desenvolvimento ativo, quando descartar e recriar índices é a rotina.

Como os analisadores são usados para indexar os termos, você deve atribuir um analisador quando o campo é criado. Na verdade, a atribuição de **analisador** ou **indexAnalyzer** a um campo que já foi criado fisicamente não é permitida (embora você possa alterar a propriedade **searchAnalyzer** a qualquer momento sem impacto no índice).

Para alterar o analisador de um campo existente, você precisará [Recompilar o índice inteiramente](search-howto-reindex.md) (não é possível recriar campos individuais). Para índices em produção, você pode adiar uma recompilação criando um novo campo com a nova atribuição do analisador e começar a usá-lo no lugar do antigo. Use [Atualizar índice](/rest/api/searchservice/update-index) para incorporar o novo campo e [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) para populá-lo. Posteriormente, como parte da manutenção planejada do índice, será possível limpar o índice para remover campos obsoletos.

Para adicionar um novo campo a um índice existente, chame [Atualizar índice](/rest/api/searchservice/update-index) para adicionar o campo e [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) para preenchê-lo.

Para adicionar um analisador personalizado a um índice existente, passe o sinalizador **allowIndexDowntime** no [índice de atualização](/rest/api/searchservice/update-index) se desejar evitar esse erro:

*"Atualização de índice não permitida porque isso causaria tempo de inatividade. Para adicionar novos analisadores, criadores, filtros de token ou filtros de caractere a um índice existente, defina o parâmetro de consulta ' allowIndexDowntime ' como ' true ' na solicitação de atualização de índice. Observe que essa operação colocará o índice offline por pelo menos alguns segundos, fazendo com que as solicitações de indexação e consulta falhem. O desempenho e a disponibilidade de gravação do índice podem ser prejudicados por vários minutos após o índice ser atualizado ou mais tempo para índices muito grandes. "*

## <a name="recommendations-for-working-with-analyzers"></a>Recomendações para trabalhar com analisadores

Esta seção oferece sugestões sobre como trabalhar com analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura/gravação, a menos que você tenha requisitos específicos

O Pesquisa Cognitiva do Azure permite que você especifique diferentes analisadores para indexação e pesquisa por meio de propriedades de campo **indexAnalyzer** e **searchAnalyzer** adicionais. Se não forem especificados, o analisador definido com a propriedade **analyzer** será usado para indexação e pesquisa. Se o **Analyzer** não for especificado, o analisador padrão Lucene padrão será usado.

Uma regra geral é usar o mesmo analisador para indexação e consulta, a menos que requisitos específicos estabeleçam o contrário. Teste cuidadosamente. Quando o processamento de texto for diferente no momento de pesquisa e de indexação, você corre o risco de haver incompatibilidade entre os termos de consulta e os termos de indexação quando as configurações do analisador de pesquisa e de indexação não estiverem alinhadas.

### <a name="test-during-active-development"></a>Testar durante o desenvolvimento ativo

Substituir o analisador padrão requer um rebuild de índice. Se possível, escolha os analisadores a usar durante o desenvolvimento ativo antes de acumular um índice para produção.

### <a name="inspect-tokenized-terms"></a>Inspecionar termos indexados

Se uma pesquisa não retornar os resultados esperados, o cenário mais provável é discrepâncias de token entre as entradas de termo na consulta e os termos indexados no índice. Se os tokens não forem os mesmos, as correspondências não se materializarão. Para inspecionar a saída do indexador, é recomendável usar a [API Analisar](/rest/api/searchservice/test-analyzer) como uma ferramenta de investigação. A resposta é composta por tokens gerados por um analisador específico.

<a name="examples"></a>

## <a name="rest-examples"></a>Exemplos de REST

Os exemplos abaixo mostram definições do analisador para alguns cenários principais.

+ [Exemplo de analisador personalizado](#Custom-analyzer-example)
+ [Atribuir analisadores a um exemplo de campo](#Per-field-analyzer-assignment-example)
+ [Misturando analisadores para indexação e pesquisa](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exemplo de analisador de linguagem](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exemplo de analisador personalizado

Este exemplo ilustra uma definição de analisador com opções personalizadas. Opções personalizadas para filtros de caractere, tokenizadores e filtros de token são especificadas separadamente como constructos nomeados e, depois, referenciadas na definição do analisador. Elementos predefinidos são usados no estado em que se encontram e simplesmente referenciados por nome.

Percorrendo este exemplo:

* Analisadores são uma propriedade da classe do campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Ele pode ser levemente personalizado (por exemplo, personalizando uma única opção em um filtro) ou personalizado em vários locais.
* Nesse caso, o analisador personalizado é "my_analyzer" que, por sua vez, usa um tokenizador padrão personalizado "my_standard_tokenizer" e dois filtros de token: filtro de letras minúsculas e filtro asciifolding personalizado "my_asciifolding".
* Ele também define dois filtros de char personalizados “map_dash” e “remove_whitespace”. O primeiro deles substitui todos os traços por sublinhados, enquanto o segundo remove todos os espaços. Os espaços precisam ser codificados com UTF-8 nas regras de mapeamento. Os filtros de char são aplicados antes da geração de tokens e afetarão os tokens resultantes (o tokenizador padrão faz uma quebra com traços e espaços, mas não com sublinhado).

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Exemplo de atribuição do analisador por campo

O analisador Standard é o padrão. Suponha que você deseja substituir o padrão com um analisador predefinido diferente, como o analisador de padrões. Se você não está definindo opções personalizadas, você só precisa especificá-lo pelo nome na definição do campo.

O elemento "analisador" substitui o analisador Standard, campo por campo. Não há nenhuma substituição global. Neste exemplo, `text1` usa o analisador de padrões; já `text2`, que não especifica um analisador, usa o padrão.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Misturando analisadores para operações de indexação e de pesquisa

As APIs incluem atributos de índice adicionais para especificar diferentes analisadores para indexação e pesquisa. Os atributos **searchAnalyzer** e **indexAnalyzer** devem ser especificados como um par, substituindo o atributo **analyzer** individual.


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Exemplo de analisador de linguagem

Campos que contêm cadeias de caracteres em idiomas diferentes podem usar um analisador de idioma, enquanto outros campos mantêm o padrão (ou usam algum outro analisador predefinido ou personalizado). Se você usar um analisador de idioma, ele deverá ser usado para operações de indexação e de pesquisa. Campos que usam um analisador de linguagem não podem ter analisadores diferentes para indexação e pesquisa.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>Exemplos do C#

Se você estiver usando os exemplos de código do SDK do .NET, poderá acrescentar esses exemplos para usar ou configurar os analisadores.

+ [Atribuir um analisador interno](#Assign-a-language-analyzer)
+ [Configurar um analisador](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Atribuir um analisador de linguagem

Qualquer analisador usado como está, sem configuração, é especificado em uma definição de campo. Não há nenhum requisito para criar uma entrada na seção **[analisadores]** do índice. 

Os analisadores de idiomas são usados no estado em que se encontram. Para usá-los, chame [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer), especificando o tipo [LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) fornecendo um analisador de texto com suporte no Azure pesquisa cognitiva.

Os analisadores personalizados são especificados de forma semelhante na definição de campo, mas para que isso funcione, você deve especificar o analisador na definição de índice, conforme descrito na próxima seção.

```csharp
    public partial class Hotel
    {
       . . . 
        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(AnalyzerName = "url-analyze")]
        public string Url { get; set; }
      . . .
    }
```

<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definir um analisador personalizado

Quando a personalização ou configuração é necessária, adicione uma construção do analisador a um índice. Depois de defini-lo, você pode adicioná-lo à definição de campo, conforme demonstrado no exemplo anterior.

Crie um objeto [CustomAnalyzer](/dotnet/api/azure.search.documents.indexes.models.customanalyzer) . Um analisador personalizado é uma combinação definida pelo usuário de um criador conhecido, zero ou mais filtro de token e zero ou mais nomes de filtro de caracteres:

+ [CustomAnalyzer. criador](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer)
+ [CustomAnalyzer.TokenFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenfilters)
+ [CustomAnalyzer.CharFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.charfilters)

O exemplo a seguir cria um analisador personalizado chamado "URL-Analyze" que usa o [uax_url_email criador](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer) e o [filtro de token em minúsculas](/dotnet/api/microsoft.azure.search.models.tokenfiltername.lowercase).

```csharp
private static void CreateIndex(string indexName, SearchIndexClient adminClient)
{
   FieldBuilder fieldBuilder = new FieldBuilder();
   var searchFields = fieldBuilder.Build(typeof(Hotel));

   var analyzer = new CustomAnalyzer("url-analyze", "uax_url_email")
   {
         TokenFilters = { TokenFilterName.Lowercase }
   };

   var definition = new SearchIndex(indexName, searchFields);

   definition.Analyzers.Add(analyzer);

   adminClient.CreateOrUpdateIndex(definition);
}
```

Para obter mais exemplos, consulte [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

## <a name="next-steps"></a>Próximas etapas

+ Examine nossa explicação abrangente sobre [como funciona a pesquisa de texto completo no Azure pesquisa cognitiva](search-lucene-query-architecture.md). Esse artigo usa exemplos para explicar comportamentos que, à primeira vista, podem parecer contraintuitivos.

+ Tente outras sintaxes de consulta a partir da seção de exemplo [Pesquisar documentos](/rest/api/searchservice/search-documents#bkmk_examples) da [sintaxe de consulta simples](query-simple-syntax.md) no gerenciador de pesquisa no portal.

+ Saiba como aplicar [analisadores léxicos específico do idioma](index-add-language-analyzers.md).

+ [Configurar analisadores personalizados](index-add-custom-analyzers.md) para o mínimo de processamento ou processamento especializado em campos individuais.

## <a name="see-also"></a>Consulte também

 [API REST para pesquisar documentos](/rest/api/searchservice/search-documents) 

 [Sintaxe de consulta simples](query-simple-syntax.md) 

 [Sintaxe de consulta Lucene completa](query-lucene-syntax.md) 
 
 [Controlar os resultados da pesquisa](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png