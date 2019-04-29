---
title: Analisadores para linguística e processamento de texto - Azure Search
description: Atribua analisadores a campos de pesquisa de texto pesquisáveis em um índice para substituir Lucene Standard padrão com alternativas personalizadas específicas a um idioma ou predefinidas.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: e3738980206277587ca367339d75da4f3faa643a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61316822"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analisadores para processamento de texto no Azure Search

Um *analisador* é um componente do [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cadeias de caracteres de consulta e documentos indexados. Analisadores diferentes manipulam texto de maneiras diferentes, dependendo do cenário. Os analisadores de idioma processam texto usando regras linguísticas a fim de melhorar a qualidade da pesquisa, enquanto outros analisadores realizam tarefas mais básicas como conversão de caracteres em minúsculas, por exemplo. 

Os analisadores de idioma são usados com mais frequência e há um analisador de idioma padrão atribuído a cada campo pesquisável em um índice do Azure Search. As transformações de idioma a seguir são normais durante a análise de texto:

+ Palavras não essenciais (palavras irrelevantes) e sinais de pontuação são removidos.
+ Frases e palavras hifenizadas são divididos em partes componentes.
+ Palavras de letras maiúsculas estão em minúsculas.
+ As palavras são reduzidas para formas raiz, de modo que uma correspondência pode ser encontrada independentemente da conjugação.

Os analisadores de idioma convertem um texto de entrada em suas formas primitiva ou raiz, que são eficientes para armazenar e recuperar informações. A conversão ocorre durante a indexação, quando o índice é criado e novamente durante a pesquisa quando o índice é lido. Haverá maior probabilidade de obter os resultados da pesquisa que você espera se usar o mesmo analisador para ambas as operações.

## <a name="default-analyzer"></a>Analisador padrão  

O Azure Search usa o [Analisador Padrão do Apache Lucene (standard lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) como o padrão, que divide o texto em elementos seguindo as regras de ["Segmentação de texto Unicode"](https://unicode.org/reports/tr29/). Além disso, o analisador padrão converte todos os caracteres em sua forma em letras minúsculas. Documentos indexados e termos de pesquisa são submetidos a análise durante a indexação e o processamento de consultas.  

Ele é usado automaticamente em cada campo pesquisável. Você pode substituir o padrão campo por campo. Os analisadores alternativos podem englobar um [analisador de idioma](index-add-language-analyzers.md), um [analisador personalizado](index-add-custom-analyzers.md) ou um analisador predefinido da [lista de analisadores disponíveis](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Tipos de analisadores

A lista a seguir descreve quais analisadores estão disponíveis no Azure Search.

| Category | DESCRIÇÃO |
|----------|-------------|
| [Analisador Lucene padrão](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Padrão. Nenhuma especificação ou a configuração é necessária. Esse analisador de uso geral funciona bem para a maioria dos idiomas e cenários.|
| Analisadores predefinidos | Oferecidos como um produto acabado destinado a ser usado no estado em que se encontra. <br/>Há dois tipos: especializado e de idioma. O que os torna "predefinidos" é que você os referencia por nome, sem nenhuma configuração ou personalização. <br/><br/>[Analisadores especializados (independentes de idioma)](index-add-custom-analyzers.md#AnalyzerTable) são usados quando as entradas de texto exigem processamento especializado ou o mínimo de processamento. Analisadores de idioma não predefinidos incluem **Asciifolding**, **Palavra-chave**, **Padrão**, **Simples**, **Interromper**, **Espaço em branco**.<br/><br/>[Analisadores de idioma](index-add-language-analyzers.md) são usados quando você precisa de suporte linguístico avançado para idiomas individuais. O Azure Search dá suporte a 35 analisadores de idioma Lucene e 50 analisadores de processamento de idioma natural Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refere-se a uma configuração definida por usuário de uma combinação de elementos existentes, consistindo em um tokenizer (obrigatório) e filtros opcionais (char ou token).|

Alguns analisadores predefinidos, como **Padrão** ou de **Parar**, dão suporte a um conjunto limitado de opções de configuração. Para definir essas opções, você efetivamente cria um analisador personalizado, consistindo de um analisador predefinido e uma das opções alternativas documentadas na [Referência do analisador predefinido](index-add-custom-analyzers.md#AnalyzerTable). Como ocorre com qualquer configuração personalizada, forneça sua nova configuração com um nome, como *myPatternAnalyzer* para distingui-lo do analisador de padrões Lucene.

## <a name="how-to-specify-analyzers"></a>Como especificar os analisadores

1. (somente para os analisadores personalizados) Crie uma seção chamada **analyzer** na definição do índice. Para saber mais, confira [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [Adicionar analisadores personalizados](index-add-custom-analyzers.md).

2. Em uma [definição de campo](https://docs.microsoft.com/rest/api/searchservice/create-index) no índice, defina a propriedade **analyzer** do campo como o nome de um analisador de destino (por exemplo, `"analyzer" = "keyword"`). Os valores válidos incluem um nome de analisador predefinido, um analisador de idioma ou um analisador personalizado também definido no esquema de índice. Planeje atribuir o analisador na fase de definição de índice antes que o índice seja criado no serviço.

3. Opcionalmente, em vez de uma propriedade **analyzer**, é possível definir diferentes analisadores para indexação e consulta usando os parâmetros de campo **indexAnalyzer** e **searchAnalyzer**. Você usaria diferentes analisadores para preparação de dados e recuperação se uma dessas atividades exigisse uma transformação específica não necessária pela outra.

Não é permitido atribuir **analyzer** ou **indexAnalyzer** a um campo que já tenha sido criado fisicamente. Se alguma coisa aqui não ficou clara, examine a tabela a seguir para obter um detalhamento de quais ações exigem uma recompilação e por quê.
 
 | Cenário | Impacto | Etapas |
 |----------|--------|-------|
 | Adicionar um novo campo | minimal | Se o campo ainda não existir no esquema, não ocorrerá nenhuma revisão de campo, pois ele ainda não tem uma presença física no seu índice. É possível usar [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) para adicionar um novo campo a um índice existente e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para populá-lo.|
 | Adicione um **analyzer** ou **indexAnalyzer** a um campo indexado existente. | [rebuild](search-howto-reindex.md) | O índice invertido para esse campo deve ser recriado do zero e o conteúdo desses campos deve ser reindexado. <br/> <br/>Para índices em desenvolvimento ativo, [exclua](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [crie](https://docs.microsoft.com/rest/api/searchservice/create-index) o índice para acompanhar a nova definição de campo. <br/> <br/>Para índices em produção, é possível adiar uma recompilação criando um novo campo para fornecer a definição revisada e começar a usá-la no lugar de uma antiga. Use [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) para incorporar o novo campo e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para populá-lo. Posteriormente, como parte da manutenção planejada do índice, será possível limpar o índice para remover campos obsoletos. |

## <a name="when-to-add-analyzers"></a>Quando adicionar analisadores

O melhor momento para adicionar e atribuir analisadores é durante o desenvolvimento ativo, quando descartar e recriar índices é a rotina.

À medida que uma definição de índice se solidifica, é possível acrescentar novos constructos de análise a um índice, mas será necessário passar o sinalizador **allowIndexDowntime** para [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) se você desejar evitar esse erro:

*"A atualização do índice não é permitida, porque causaria tempo de inatividade. Para adicionar novos analisadores, tokenizers, filtros de token ou de caractere a um índice existente, defina o parâmetro de consulta 'allowIndexDowntime' como 'true' na solicitação de atualização de índice. Observe que essa operação deixará seu índice offline por pelo menos alguns segundos, fazendo com que suas solicitações de indexação e de consulta falhem. O desempenho e a disponibilidade de gravação do índice podem ser prejudicados por vários minutos após o índice ser atualizado ou por mais tempo em caso de índices muito grandes.”*

O mesmo se aplica ao atribuir um analisador a um campo. Um analisador é parte integrante da definição do campo; portanto, é possível adicioná-lo apenas quando o campo é criado. Se desejar adicionar analisadores a campos existentes, será necessário [remover e recompilar](search-howto-reindex.md) o índice ou adicionar um novo campo com o analisador desejado.

Conforme observado, uma exceção é a variante **searchAnalyzer**. Das três maneiras de especificar analisadores (**analyzer**, **indexAnalyzer**, **searchAnalyzer**), somente o atributo **searchAnalyzer** pode ser alterado em um campo existente.

## <a name="recommendations-for-working-with-analyzers"></a>Recomendações para trabalhar com analisadores

Esta seção oferece sugestões sobre como trabalhar com analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura/gravação, a menos que você tenha requisitos específicos

O Azure Search permite especificar diferentes analisadores para indexação e pesquisa por meio dos parâmetros de campo adicionais **indexAnalyzer** e **searchAnalyzer**. Se não forem especificados, o analisador definido com a propriedade **analyzer** será usado para indexação e pesquisa. Se `analyzer` não for especificado, o analisador padrão para Lucene Standard será usado.

Uma regra geral é usar o mesmo analisador para indexação e consulta, a menos que requisitos específicos estabeleçam o contrário. Teste cuidadosamente. Quando o processamento de texto for diferente no momento de pesquisa e de indexação, você corre o risco de haver incompatibilidade entre os termos de consulta e os termos de indexação quando as configurações do analisador de pesquisa e de indexação não estiverem alinhadas.

### <a name="test-during-active-development"></a>Testar durante o desenvolvimento ativo

Substituir o analisador padrão requer um rebuild de índice. Se possível, escolha os analisadores a usar durante o desenvolvimento ativo antes de acumular um índice para produção.

### <a name="inspect-tokenized-terms"></a>Inspecionar termos indexados

Se uma pesquisa não retornar os resultados esperados, o cenário mais provável é discrepâncias de token entre as entradas de termo na consulta e os termos indexados no índice. Se os tokens não forem os mesmos, as correspondências não se materializarão. Para inspecionar a saída do indexador, é recomendável usar a [API Analisar](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) como uma ferramenta de investigação. A resposta é composta por tokens gerados por um analisador específico.

### <a name="compare-english-analyzers"></a>Comparar analisadores em inglês

A [Demonstração do analisador de pesquisa](https://alice.unearth.ai/) é um aplicativo de demonstração de terceiros que mostra uma comparação lado a lado do analisador Lucene padrão, do analisador do idioma inglês do Lucene e do processador de idioma natural inglês da Microsoft. O índice é fixo; ele contém o texto de uma história popular. Para cada entrada de pesquisa fornecida, os resultados de cada analisador são exibidos nos painéis adjacentes, dando uma ideia de como cada analisador processa a mesma cadeia de caracteres. 

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

~~~~
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
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Exemplo de atribuição do analisador por campo

O analisador Standard é o padrão. Suponha que você deseja substituir o padrão com um analisador predefinido diferente, como o analisador de padrões. Se você não está definindo opções personalizadas, você só precisa especificá-lo pelo nome na definição do campo.

O elemento "analisador" substitui o analisador Standard, campo por campo. Não há nenhuma substituição global. Neste exemplo, `text1` usa o analisador de padrões; já `text2`, que não especifica um analisador, usa o padrão.

~~~~
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
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Misturando analisadores para operações de indexação e de pesquisa

As APIs incluem atributos de índice adicionais para especificar diferentes analisadores para indexação e pesquisa. Os atributos **searchAnalyzer** e **indexAnalyzer** devem ser especificados como um par, substituindo o atributo **analyzer** individual.


~~~~
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
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Exemplo de analisador de linguagem

Campos que contêm cadeias de caracteres em idiomas diferentes podem usar um analisador de idioma, enquanto outros campos mantêm o padrão (ou usam algum outro analisador predefinido ou personalizado). Se você usar um analisador de idioma, ele deverá ser usado para operações de indexação e de pesquisa. Campos que usam um analisador de linguagem não podem ter analisadores diferentes para indexação e pesquisa.

~~~~
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
~~~~

## <a name="c-examples"></a>C#exemplos

Se você estiver usando os exemplos de código do SDK do .NET, você pode acrescentar esses exemplos para usar ou configurar os analisadores.

+ [Atribuir um analisador interno](#Assign-a-language-analyzer)
+ [Configure um analisador](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Atribuir um analisador de idioma

Qualquer analisador que é usado como-está, sem nenhuma configuração, é especificado em uma definição de campo. Não há nenhum requisito para a criação de uma construção de analisador. 

Este exemplo atribui os analisadores Microsoft English e francês para campos de descrição. É um trecho de código obtido de uma definição maior do índice de hotéis, criando usando a classe de Hotel no arquivo do hotels.cs a [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) exemplo.

Chame [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), especificando as [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) tipo que fornece um analisador de texto com suporte no Azure Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definir um analisador personalizado

Quando a personalização ou configuração é necessária, você precisará adicionar uma construção de analisador para um índice. Depois de você defini-lo, você pode adicioná-lo a definição de campo conforme demonstrado no exemplo anterior.

Criar uma [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) objeto. Para obter mais exemplos, consulte [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Próximas etapas

+ Examine nossa explicação abrangente sobre [como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md). Esse artigo usa exemplos para explicar comportamentos que, à primeira vista, podem parecer contraintuitivos.

+ Tente outras sintaxes de consulta a partir da seção de exemplo [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) da [sintaxe de consulta simples](query-simple-syntax.md) no gerenciador de pesquisa no portal.

+ Saiba como aplicar [analisadores léxicos específico do idioma](index-add-language-analyzers.md).

+ [Configurar analisadores personalizados](index-add-custom-analyzers.md) para o mínimo de processamento ou processamento especializado em campos individuais.

+ [Compare os analisadores padrão e inglês](https://alice.unearth.ai/) em painéis adjacentes neste site da Web de demonstração. 

## <a name="see-also"></a>Consulte também

 [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxe de consulta simples](query-simple-syntax.md) 

 [Sintaxe de consulta Lucene completa](query-lucene-syntax.md) 
 
 [Controlar os resultados da pesquisa](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
