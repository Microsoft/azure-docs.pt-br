---
title: Adicionar analisadores personalizados — Azure Search
description: Modifique os criadores de token de texto e os filtros de caracteres usados nas consultas de pesquisa de texto completo do Azure Search.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 9a15078c953c1fab40ad521eff079a623c93b9d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914901"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Adicionar analisadores personalizados a um índice do Azure Search

Um *analisador personalizado* é um tipo específico de [analisador de texto](search-analyzers.md) composto por uma combinação de um criador de token existente e  filtros opcionais definida pelo usuário. Combinando criadores de token e filtros de novas maneiras, você pode personalizar o processamento de texto no mecanismo de pesquisa para obter resultados específicos. Por exemplo, você poderia criar um analisador personalizado com um *filtro de caracteres* para remover a marcação HTML antes que as entradas de texto sejam tokenizadas.

 Você pode definir vários analisadores personalizados para variar a combinação de filtros, mas cada campo só pode usar um analisador para análise de indexação e outro para a análise de pesquisa. Para ver qual seria a aparência de um analisador de cliente, consulte [Exemplo de analisador personalizado](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Visão geral

 Simplificando, a função de um [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) é processar e armazenar documentos de uma maneira que permita consulta e recuperação eficientes. Em um alto nível, tudo se resume a extrair palavras importantes de documentos, colocá-las em um índice e usar o índice para localizar documentos que correspondam às palavras de uma determinada consulta. O processo de extração de palavras de documentos e consultas de pesquisa é chamado de *análise léxica*. Os componentes que executam a análise léxica são chamados de *analisadores*.

 No Azure Search, você pode escolher entre um conjunto de analisadores agnósticos de idioma predefinidos na tabela [Analisadores](#AnalyzerTable) ou analisadores específicos de idioma listados em [Analisadores de idioma (API REST do Azure Search Service)](index-add-language-analyzers.md). Você também tem a opção para definir os próprios analisadores personalizados.  

 Um analisador personalizado permite que você assuma o controle do processo de conversão de texto em tokens indexáveis e pesquisáveis. É uma configuração definida pelo usuário composta por um único criador de token predefinido, um ou mais filtros de token e um ou mais filtros de caracteres. O criador de token é responsável por quebrar o texto em tokens, e os filtros de token são responsáveis por modificar tokens emitidos pelo criador de token. Filtros de caracteres são aplicados para preparar o texto de entrada antes de ser processado pelo criador de tokens. Por exemplo, um filtro de caracteres pode substituir certos caracteres ou símbolos.

 Entre os cenários populares habilitados pelos analisadores personalizados estão:  

- Pesquisa fonética. Adicione um filtro fonético para permitir a pesquisa com base no som de uma palavra, e não na ortografia.  

- Desabilitar análise léxica. Use o analisador de palavra-chave para criar campos de pesquisa que não são analisados.  

- Pesquisa rápida de prefixo/sufixo. Adicione o filtro de token Edge N-gram para indexar prefixos de palavras a fim de habilitar a correspondência rápida de prefixo. Combine-a com o filtro de token Inverso para realizar a correspondência de sufixo.  

- Criação de tokens personalizada. Por exemplo, use o criador de token Whitespace para dividir as frases em tokens usando o espaço em branco como um delimitador  

- Folding ASCII. Adicione o filtro Folding ASCII padrão para normalizar diacríticos como ö ou ê em termos de pesquisa.  

  Esta página fornece uma lista de analisadores, criadores de token, filtros de token e filtros de caracteres. Você também pode encontrar uma descrição das alterações na definição de índice, com um exemplo de uso. Para saber mais sobre a tecnologia subjacente utilizada na implementação do Azure Search, confira [Resumo do pacote de análise (Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html). Para obter exemplos de configurações do analisador, confira [Adicionar analisadores no Azure Search](search-analyzers.md#examples).

## <a name="validation-rules"></a>Regras de validação  
 Os nomes dos analisadores, criadores de token, filtros de token e filtros de caracteres devem ser exclusivos e não podem ser iguais aos dos analisadores, criadores de token, filtros de token ou filtros de caracteres predefinidos. Confira a [Referência de Propriedade](#PropertyReference) para nomes que já estão em uso.

## <a name="create-custom-analyzers"></a>Criar analisadores personalizados
 Você pode definir analisadores personalizados na hora da criação do índice. A sintaxe para especificar um analisador personalizado é descrita nesta seção. Você também pode se familiarizar com a sintaxe examinando exemplos de definições em [Adicionar analisadores no Azure Search](search-analyzers.md#examples).  

 Uma definição de analisador inclui um nome, um tipo, um ou mais filtros de caracteres, um máximo de um criador de token e um ou mais filtros de token para processamento de pós-criação de token. Os filtros de caracteres são aplicados antes da criação de token. Filtros de token e filtros de caracteres são aplicados da esquerda para a direita.

 O `tokenizer_name` é o nome de um criador de token `token_filter_name_1` e `token_filter_name_2` são os nomes dos filtros de token, e `char_filter_name_1` e `char_filter_name_2` são os nomes de char filtros (consulte a [criadores](#Tokenizers), [ Filtros de token](#TokenFilters) e tabelas para obter valores válidos de filtros de Char).

A definição do analisador é uma parte do índice maior. Confira informações sobre o resto do índice em [Criar API de índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  Os analisadores personalizados que você cria não são expostos no Portal do Azure. A única maneira de adicionar um analisador personalizado é por meio do código que faz chamadas para a API durante a definição de um índice.  

 Dentro de uma definição de índice, você pode colocar essa seção em qualquer lugar no corpo de uma solicitação de criação de índice, mas geralmente ela ocorre no final:  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

As definições para filtros de caracteres, criadores de token e filtros de token serão adicionadas ao índice somente se você estiver definindo opções personalizadas. Para usar um filtro ou criador de token existente no estado em que ele se encontra, especifique-o por nome na definição do analisador.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testar analisadores personalizados

Você pode usar a **operação Analisador de Testes** na [API REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para verificar como um analisador divide um determinado texto em tokens.

**Solicitação**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Resposta**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Atualizar analisadores personalizados

Quando um analisador, um criador de token, um filtro de token ou um filtro de caracteres é definido, ele não pode ser modificado. Outros novos poderão ser adicionados a um índice existente apenas se o sinalizador `allowIndexDowntime` estiver definido como true na solicitação de atualização de índice:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Essa operação deixa seu índice offline por pelo menos alguns segundos, fazendo com que suas solicitações de indexação e de consulta falhem. O desempenho e a disponibilidade de gravação do índice podem ser prejudicados por vários minutos após o índice ser atualizado, ou por mais tempo em caso de índices muito grandes, mas esses efeitos são temporários e acabam se resolvendo sozinhos.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Referência de analisador

A tabela abaixo lista as propriedades de configuração para a seção de analisadores, criadores de token, filtros de token e filtro de caracteres de uma definição de índice. A estrutura de um analisador, criador de token ou filtro em seu índice é composta desses atributos. Para informações de atribuição de valor, confira [Referência de Propriedade](#PropertyReference).

### <a name="analyzers"></a>Analisadores

Para os analisadores, os atributos de índice variam dependendo se você estiver usando analisadores predefinidos ou personalizados.

#### <a name="predefined-analyzers"></a>Analisadores predefinidos

|||  
|-|-|  
|NOME|Deve conter apenas letras, números, espaços, traços ou sublinhados, pode começar e terminar apenas com caracteres alfanuméricos e está limitado a 128 caracteres.|  
|Type|Tipo de analisador da lista de analisadores com suporte. Confira a coluna **analyzer_type** na tabela [Analisadores](#AnalyzerTable) abaixo.|  
|Opções|Devem ser opções válidas de um analisador predefinido listado na tabela [Analisadores](#AnalyzerTable) abaixo.|  

#### <a name="custom-analyzers"></a>Analisadores personalizados

|||  
|-|-|  
|NOME|Deve conter apenas letras, números, espaços, traços ou sublinhados, pode começar e terminar apenas com caracteres alfanuméricos e está limitado a 128 caracteres.|  
|Type|Deve ser "#Microsoft.Azure.Search.CustomAnalyzer".|  
|CharFilters|Defina como um dos filtros de caracteres predefinidos listados na tabela [Filtros de caracteres](#CharFilter) ou um filtro de caracteres personalizado especificado na definição do índice.|  
|Gerador de token|Obrigatório. Defina como um dos criadores de token predefinidos listados na tabela [Criadores de token](#Tokenizers) abaixo ou um criador de token personalizado especificado na definição do índice.|  
|Filtros de token|Defina como um dos filtros de token predefinidos listados na tabela [Filtros de token](#TokenFilters) ou um filtro de token personalizado especificado na definição do índice.|  

<a name="CharFilter"></a>

### <a name="char-filters"></a>Filtros de caracteres

 Um filtro de caracteres é usado para preparar o texto de entrada antes de ser processado pelo criador de tokens. Por exemplo, ele pode substituir certos caracteres ou símbolos. Você pode ter vários filtros de caracteres em um analisador personalizado. Filtros de caracteres são executados na ordem em que estão listados.  

|||  
|-|-|  
|NOME|Deve conter apenas letras, números, espaços, traços ou sublinhados, pode começar e terminar apenas com caracteres alfanuméricos e está limitado a 128 caracteres.|  
|Type|Tipo de filtro de caracteres da lista de filtros de caracteres com suporte. Confira a coluna **char_filter_type** na tabela [Filtros de caracteres](#CharFilter) abaixo.|  
|Opções|Deve ser uma opção válida de um determinado tipo de [Filtros de caracteres](#CharFilter).|  

### <a name="tokenizers"></a>Criadores de token

 Um criador de token divide o texto contínuo em uma sequência de tokens, assim como quebrar uma frase em palavras.  

 Você pode especificar exatamente um criador de token por analisador personalizado. Se precisar de mais de um criador de token, você pode criar vários analisadores personalizados e atribuí-los campo por campo em seu esquema de índice.  
Um analisador personalizado pode usar um criador de token predefinido com opções personalizadas ou padrão.  

|||  
|-|-|  
|NOME|Deve conter apenas letras, números, espaços, traços ou sublinhados, pode começar e terminar apenas com caracteres alfanuméricos e está limitado a 128 caracteres.|  
|Type|Nome do criador de token da lista de criadores de token com suporte. Confira a coluna **tokenizer_type** na tabela [Criadores de token](#Tokenizers) abaixo.|  
|Opções|Deve ser uma opção válida de um determinado tipo de criador de token listado na tabela [Criadores de token](#Tokenizers) abaixo.|  

### <a name="token-filters"></a>Filtros de token

 Um filtro de token é usado para filtrar ou modificar os tokens gerados por um criador de token. Por exemplo, você pode especificar um filtro de minúsculas que converte todos os caracteres em letras minúsculas.   
Você pode ter vários filtros de token em um analisador personalizado. Os filtros de token são executados na ordem em que estão listados.  

|||  
|-|-|  
|NOME|Deve conter apenas letras, números, espaços, traços ou sublinhados, pode começar e terminar apenas com caracteres alfanuméricos e está limitado a 128 caracteres.|  
|Type|Nome do filtro de token da lista de filtros de token com suporte. Confira a coluna **token_filter_type** na tabela [Filtros de token](#TokenFilters) abaixo.|  
|Opções|Deve ser [Filtros de token](#TokenFilters) de um determinado tipo de filtro de token.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Referência de propriedade

Esta seção fornece os valores válidos para atributos especificados na definição de um analisador personalizado, criador de token, filtro de caracteres ou filtro de token em seu índice. Analisadores, criadores de token e filtros que são implementados usando o Apache Lucene têm links para a documentação da API Lucene.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Referência de analisadores predefinidos

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Descrição e opções**|  
|-|-|-|  
|[Palavra-chave](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (o tipo aplica-se somente quando há opções disponíveis) |Trata todo o conteúdo de um campo como um único token. É útil para dados como códigos postais, IDs e alguns nomes de produtos.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Separa texto em termos de forma flexível por meio de um padrão de expressão regular.<br /><br /> **Opções**<br /><br /> lowercase (tipo: bool) — determina se os termos estão em minúscula. O padrão é true.<br /><br /> [pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (tipo: cadeia de caracteres) — um padrão de expressão regular para corresponder separadores de token. O padrão é \w+.<br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: cadeia de caracteres) — sinalizadores de expressão regular. O padrão é uma cadeia de caracteres vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stopwords (tipo: matriz de cadeia de caracteres) — uma lista de palavras irrelevantes. O padrão é uma lista vazia.|  
|[simpless](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(o tipo aplica-se somente quando há opções disponíveis) |Divide o texto em não letras e converte em minúsculas. |  
|[standard](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Também conhecido como standard.lucene)|StandardAnalyzer|Analisador padrão Lucene, composto pelo criador de token padrão, filtro de letras minúsculas e interrupção de filtro.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) — o comprimento máximo do token. O padrão é 255. Tokens maiores do que o tamanho máximo são divididos. O comprimento máximo do token que pode ser usado é de 300 caracteres.<br /><br /> stopwords (tipo: matriz de cadeia de caracteres) — uma lista de palavras irrelevantes. O padrão é uma lista vazia.|  
|standardasciifolding.Lucene|(o tipo aplica-se somente quando há opções disponíveis) |Analisador padrão com Filtro Ascii. |  
|[stop](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Divide o texto em não letras, aplica os filtros de token em minúsculas e de palavras irrelevantes.<br /><br /> **Opções**<br /><br /> stopwords (tipo: matriz de cadeia de caracteres) — uma lista de palavras irrelevantes. O padrão é uma lista predefinida para Inglês. |  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(o tipo aplica-se somente quando há opções disponíveis) |Um analisador que usa o criador de token whitespace. Os tokens com mais de 255 caracteres são divididos.|  

 <sup>1</sup> Os tipos de analisadores são sempre prefixados em código com "#Microsoft.Azure.Search", ou seja, "PatternAnalyzer" na verdade seria "#Microsoft.Azure.Search.PatternAnalyzer". Removemos o prefixo para encurtar, mas ele é necessário em seu código. 
 
O analyzer_type é fornecido apenas para analisadores que podem ser personalizados. Se não há opções, como é o caso do analisador de palavras-chave, não há nenhum tipo associado do #Microsoft.Azure.Search.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Referência de filtros de caracteres

Na tabela abaixo, filtros de caracteres que são implementados usando o Apache Lucene têm links para a documentação da API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Descrição e opções**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Um filtro de caracteres que tenta remover constructos de HTML.|  
|[mapeamento](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Um filtro de caracteres que aplica mapeamentos definidos com a opção de mapeamentos. A correspondência é gananciosa (a correspondência de padrões mais longa em um determinado ponto vence). A substituição é permitida como a cadeia de caracteres vazia.<br /><br /> **Opções**<br /><br /> mappings (tipo: cadeia de caracteres) — uma lista de mapeamentos do seguinte formato: "a=>b" (todas as ocorrências do caracter "a" são substituídas pelo caracter "b"). Obrigatório.|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Um filtro de caracteres que substitui os caracteres na cadeia de entrada. Ele usa uma expressão regular para identificar sequências de caracteres para preservar e um padrão de substituição para identificar caracteres para substituir. Por exemplo, input text = "aa  bb aa bb", pattern="(aa)\\\s+(bb)" replacement="$1#$2", result = "aa#bb aa#bb".<br /><br /> **Opções**<br /><br /> pattern (tipo: cadeia de caracteres) — obrigatório.<br /><br /> replacement (tipo: cadeia de caracteres) — obrigatório.|  

 <sup>1</sup> Os tipos de filtros de caracteres são sempre prefixados em código com "#Microsoft.Azure.Search", ou seja, "MappingCharFilter" na verdade seria "#Microsoft.Azure.Search.MappingCharFilter. Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo em seu código. Observe que char_filter_type é fornecido apenas para filtros que podem ser personalizados. Se não ha opções, como é o caso de html_strip, não há nenhum tipo associado do #Microsoft.Azure.Search.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Referência de criadores de token

Na tabela abaixo, os criadores de token que são implementados usando o Apache Lucene têm links para a documentação da API Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Descrição e opções**|  
|-|-|-|  
|[clássico](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|O criador de token baseado em gramática é adequado para processar a maioria dos documentos no idioma europeu.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) — o comprimento máximo do token. Padrão: 255, máximo: 300. Tokens maiores do que o tamanho máximo são divididos.|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Cria tokens para a entrada a partir de uma borda em n-gramas de determinados tamanhos.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) — padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) — padrão: 2, máximo: 300. Deve ser maior que minGram.<br /><br /> tokenChars (tipo: matriz de cadeia de caracteres) — classes de caracteres para manter nos tokens. Valores permitidos: <br />“letra”, “dígito”, “espaço em branco”, “pontuação”, “símbolo”. O padrão é uma matriz vazia, mantém todos os caracteres. |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Emite a entrada inteira como um único token.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) — o comprimento máximo do token. Padrão: 256, máximo: 300. Tokens maiores do que o tamanho máximo são divididos.|  
|[letter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Divide o texto em não letras. Os tokens com mais de 255 caracteres são divididos.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Divide o texto em não letras e converte em minúsculas. Os tokens com mais de 255 caracteres são divididos.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Divide o texto usando regras específicas de idioma.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) — o comprimento máximo do token, padrão: 255, máximo: 300. Tokens maiores do que o tamanho máximo são divididos. Tokens mais longos que 300 caracteres são divididos em tokens de 300 caracteres, e estes são divididos de acordo com o maxTokenLength definido.<br /><br />isSearchTokenizer (tipo: bool) — defina como true se for usado como o criador de token de pesquisa, defina como false se for usado como criador de token de indexação. <br /><br /> language (tipo: cadeia de caracteres) — idioma a ser usado, o padrão é "english". Entre os valores permitidos estão:<br />"bangla", "bulgarian", "catalan", "chineseSimplified",  "chineseTraditional", "croatian", "czech", "danish", "dutch", "english",  "french", "german", "greek", "gujarati", "hindi", "icelandic", "indonesian", "italian", "japanese", "kannada", "korean", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovenian", "spanish", "swedish", "tamil", "telugu", "thai", "ukrainian", "urdu", "vietnamese" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Divide o texto usando regras específicas de idioma e reduz palavras para seus formulários base<br /><br /> **Opções**<br /><br />maxTokenLength (tipo: int) — o comprimento máximo do token, padrão: 255, máximo: 300. Tokens maiores do que o tamanho máximo são divididos. Tokens mais longos que 300 caracteres são divididos em tokens de 300 caracteres, e estes são divididos de acordo com o maxTokenLength definido.<br /><br /> isSearchTokenizer (tipo: bool) — defina como true se for usado como o criador de token de pesquisa, defina como false se for usado como criador de token de indexação.<br /><br /> language (tipo: cadeia de caracteres) — idioma a ser usado, o padrão é "english". Entre os valores permitidos estão:<br />"arabic", "bangla", "bulgarian", "catalan", "croatian", "czech", "danish", "dutch", "english", "estonian", "finnish", "french", "german", "greek", "gujarati", "hebrew", "hindi", "hungarian", "icelandic", "indonesian", "italian", "kannada", "latvian", "lithuanian", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovak", "slovenian", "spanish", "swedish", "tamil", "telugu", "turkish", "ukrainian", "urdu" |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Cria tokens de entrada em n-gramas de determinados tamanhos.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) — padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) — padrão: 2, máximo: 300. Deve ser maior que minGram. <br /><br /> tokenChars (tipo: matriz de cadeia de caracteres) — classes de caracteres para manter nos tokens. Valores permitidos: "letter", "digit", "whitespace", "punctuation", "symbol". O padrão é uma matriz vazia, mantém todos os caracteres. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Criador de token para hierarquias de caminho.<br /><br /> **Opções**<br /><br /> delimiter (tipo: cadeia de caracteres) — padrão: '/.<br /><br /> replacement (tipo: cadeia de caracteres) — se definido, substitui o caractere delimitador. Tem como padrão o mesmo valor do delimitador.<br /><br /> maxTokenLength (tipo: int) — o comprimento máximo do token. Padrão: 300, máximo: 300. Caminhos mais longos que o maxTokenLength são ignorados.<br /><br /> reverse (tipo: bool) — se for true, gera o token na ordem inversa. Padrão: falso.<br /><br /> skipl (tipo: bool) — tokens iniciais a ignorar. O padrão é 0.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Este criador de token usa a correspondência de padrões de regex para criar tokens distintos.<br /><br /> **Opções**<br /><br /> pattern (tipo: cadeia de caracteres) — padrão de expressão regular. O padrão é \w+.<br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: cadeia de caracteres) — sinalizadores de expressão regular. O padrão é uma cadeia de caracteres vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> group (tipo: int) — qual grupo extrair em tokens. O padrão é -1 (divisão).|  
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Quebra o texto seguindo as [regras de Segmentação de Texto Unicode](https://unicode.org/reports/tr29/).<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) — o comprimento máximo do token. Padrão: 255, máximo: 300. Tokens maiores do que o tamanho máximo são divididos.|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Cria tokens de urls e emails como um único token.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) — o comprimento máximo do token. Padrão: 255, máximo: 300. Tokens maiores do que o tamanho máximo são divididos.|  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(o tipo aplica-se somente quando há opções disponíveis) |Divide o texto em espaços em branco. Os tokens com mais de 255 caracteres são divididos.|  

 <sup>1</sup> Os tipos de criadores de token são sempre prefixados em código com "#Microsoft.Azure.Search", ou seja, "ClassicTokenizer" na verdade seria "#Microsoft.Azure.Search.ClassicTokenizer". Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo em seu código. Observe que tokenizer_type é fornecido apenas para criadores de token que podem ser personalizados. Se não há opções, como é o caso do criador de token de letra, não há nenhum tipo associado do # Microsoft.Azure.Search.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Referência de filtros de token

Na tabela abaixo, os filtros de token que são implementados usando o Apache Lucene têm links para a documentação da API Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Descrição e opções**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Um filtro de token que aplica o normalizador em árabe para normalizar a ortografia.|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Remove todos os caracteres após um apóstrofo (incluindo o próprio apóstrofo). |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Converte caracteres Unicode alfabéticos, numéricos e simbólicos que não estão nos primeiros 127 caracteres ASCII (o bloco Unicode "Latim básico") em seus equivalentes ASCII, se existirem.<br /><br /> **Opções**<br /><br /> preserveOriginal (tipo: bool) — se true, o token original é mantido. O padrão é false.|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Forma bigramas de termos CJK que são gerados a partir do StandardTokenizer.<br /><br /> **Opções**<br /><br /> ignoreScripts (tipo: matriz de cadeia de caracteres) – scripts a ignorar. Entre os valores permitidos estão: "han", "hiragana", "katakana", "hangul". O padrão é uma lista vazia.<br /><br /> outputUnigrams (tipo: bool) — defina como true se você sempre quer gerar unigramas e bigramas. O padrão é false.|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Normaliza as diferenças de largura CJK. Transforma variantes ASCII de largura completa em variantes básicas equivalentes de latim e meia largura Katakana no kana equivalente. |  
|[clássico](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Remove os possessivos ingleses e os pontos das siglas. |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Construa bigramas para termos que ocorrem com frequência durante a indexação. Termos únicos ainda são indexados também, com bigramas sobrepostos.<br /><br /> **Opções**<br /><br /> commonWords (tipo: matriz de cadeia de caracteres) — o conjunto de palavras comuns. O padrão é uma lista vazia. Obrigatório.<br /><br /> ignoreCase (tipo: bool) — se true, a correspondência não diferencia maiúsculas e minúsculas. O padrão é false.<br /><br /> queryMode (tipo: bool) — gera bigramas, em seguida, remove palavras comuns e termos simples seguidos por uma palavra comum. O padrão é false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Decompõe palavras compostas encontradas em muitos idiomas germânicos.<br /><br /> **Opções**<br /><br /> wordList (tipo: cadeia de caracteres) — lista de palavras para correspondência. O padrão é uma lista vazia. Obrigatório.<br /><br /> minWordSize (tipo: int) — apenas palavras mais longas que isso são processadas. O padrão é 5.<br /><br /> minSubwordSize (tipo: int) — apenas subpalavras maiores que isso são geradas. O padrão é 2.<br /><br /> maxSubwordSize (tipo: int) — apenas subpalavras mais curtas que isso são geradas. O padrão é 15.<br /><br /> onlyLongestMatch (tipo: bool) — adicione somente a maior correspondência de subpalavra para saída. O padrão é false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Gera n-gramas de determinados tamanhos começando do início ou do fim de um token de entrada.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) — padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) — padrão: 2, máximo de 300. Deve ser maior que minGram.<br /><br /> side (tipo: cadeia de caracteres) — especifica de que lado da entrada o n-grama deve ser gerado. Valores permitidos: "front", "back" |  
|[elision](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Remove elisões. Por exemplo, "l'avion" (o avião) é convertido em "avion" (avião).<br /><br /> **Opções**<br /><br /> articles (tipo: matriz de cadeia de caracteres) — um conjunto de artigos a remover. O padrão é uma lista vazia. Se não houver uma lista de artigos definidos, por padrão, todos os artigos em francês serão removidos.|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Normaliza caracteres alemães de acordo com as heurísticas do algoritmo [german2](https://snowballstem.org/algorithms/german2/stemmer.html).|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Normaliza o texto em hindi para remover algumas diferenças nas variações ortográficas. |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliza a representação Unicode do texto nos idiomas indianos.
|[keep](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Um filtro de token que mantém apenas tokens com texto contido na lista especificada de palavras.<br /><br /> **Opções**<br /><br /> keepWords (tipo: cadeia de caracteres) — uma lista de palavras a manter. O padrão é uma lista vazia. Obrigatório.<br /><br /> keepWordsCase (tipo: bool) — se true, muda para minúsculas todas as palavras primeiro. O padrão é false.|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Marca termos como palavras-chave.<br /><br /> **Opções**<br /><br /> keywords (tipo: cadeia de caracteres) — uma lista de palavras para marcar como palavras-chave. O padrão é uma lista vazia. Obrigatório.<br /><br /> ignoreCase (tipo: bool) — se true, muda para minúsculas todas as palavras primeiro. O padrão é false.|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Emite cada token de entrada duas vezes: uma vez como palavra-chave e uma vez como não palavra-chave. |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Um filtro kstem de alto desempenho para o inglês. |  
|[length](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Remove palavras muito longas ou muito curtas.<br /><br /> **Opções**<br /><br /> min (tipo: int) — o número mínimo. Padrão: 0, máximo: 300.<br /><br /> max (tipo: int) — o número máximo. Padrão: 300, máximo: 300.|  
|[limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Limita o número de tokens durante a indexação.<br /><br /> **Opções**<br /><br /> maxTokenCount (tipo: int) — número máximo de tokens a produzir. O padrão é 1.<br /><br /> consumeAllTokens (tipo: bool) — se todos os tokens da entrada devem ser consumidos, mesmo se maxTokenCount for atingido. O padrão é false.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Normaliza o texto do token para letras minúsculas. |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Gera n-gramas de determinados tamanhos.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) — padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) — padrão: 2, máximo de 300. Deve ser maior que minGram.|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Usa regexes Java para emitir vários tokens, um para cada grupo de captura em um ou mais padrões.<br /><br /> **Opções**<br /><br /> patterns (ttipo: cadeia de caracteres) — uma lista de padrões para combinar com cada token. Obrigatório.<br /><br /> preserveOriginal (tipo: bool) — defina como true para retornar o token original, mesmo que um dos padrões corresponda, padrão: true |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Um filtro de token que aplica um padrão a cada token no fluxo, substituindo as ocorrências de correspondência pela cadeia de caracteres de substituição especificada.<br /><br /> **Opções**<br /><br /> pattern (tipo: cadeia de caracteres) — obrigatório.<br /><br /> replacement (tipo: cadeia de caracteres) — obrigatório.|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(o tipo aplica-se somente quando há opções disponíveis) |Aplica a normalização para persa. |  
|[phonetic](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Crie tokens para correspondências fonéticas.<br /><br /> **Opções**<br /><br /> encoder (tipo: cadeia de caracteres) — codificador fonético a ser usado. Entre os valores permitidos estão: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Padrão: "metaphone". O padrão é "metaphone".<br /><br /> Confira [encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) para obter mais informações.<br /><br /> replace (tipo: bool) — true se os tokens codificados devem substituir os tokens originais, false se eles devem ser adicionados como sinônimos. O padrão é true.|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Transforma o fluxo de token conforme o [Algoritmo de stemming de Porter](https://tartarus.org/~martin/PorterStemmer/). |  
|[reverse](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Inverte a cadeia de caracteres do token. |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Normaliza o uso de caracteres escandinavos intercambiáveis. |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Dobra os caracteres escandinavos åÅäæÄÆ->a e öÖøØ->o. Também discrimina o uso de vogais duplas aa, ae, SOL, oe e oo, deixando apenas o primeiro deles. |  
|[shingle](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Cria combinações de tokens como um único token.<br /><br /> **Opções**<br /><br /> maxShingleSize (tipo: int) — o padrão é 2.<br /><br /> minShingleSize (tipo: int) — o padrão é 2.<br /><br /> outputUnigrams (tipo: bool) — se true, o fluxo de saída contém os tokens de entrada (unigramas), bem como shingles. O padrão é true.<br /><br /> outputUnigramsIfNoShingles (tipo: bool) — se true, substitui o comportamento outputUnigrams==false para aqueles momentos em que não existem shingles disponíveis. O padrão é false.<br /><br /> tokenSeparator (tipo: cadeia de caracteres) — a cadeia de caracteres a ser usada ao unir os tokens adjacentes para formar um shingle. O padrão é " ".<br /><br /> filterToken (tipo: cadeia de caracteres) — a cadeia de caracteres a ser inserida para cada posição na qual não há token. O padrão é “_”.|  
|[snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtro Snowball Token.<br /><br /> **Opções**<br /><br /> language (tipo: cadeia de caracteres) — entre os valores permitidos estão: "armenian", "basque", "catalan", "danish", "dutch", "english", "finnish", "french", "german", "german2", "hungarian", "italian", "kp", "lovins", "norwegian", "porter", "portuguese", "romanian", "russian", "spanish", "swedish", "turkish"|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliza a representação Unicode de texto Sorani.<br /><br /> **Opções**<br /><br /> Nenhuma.|  
|stemmer|StemmerTokenFilter|Filtro de lematização específica de idioma.<br /><br /> **Opções**<br /><br /> language (tipo: cadeia de caracteres) — entre os valores permitidos estão: <br /> -   ["arabic"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["armenian"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazilian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"búlgaro"<br />-   ["catalan"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["danish"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["english"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finnish"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />-   "lightFinnish"<br />-   ["french"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-   "galician"<br />-   "minimalGalician"<br />-   ["german"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   "minimalGerman"<br />-   ["greek"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-   "hindi"<br />-   ["hungarian"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonesian"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irish"](https://snowballstem.org/otherapps/oregan/)<br />-   ["italian"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["latvian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norwegian"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portuguese"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["romanian"](https://snowballstem.org/otherapps/romanian/)<br />-   ["russian"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["spanish"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["swedish"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />-   "lightSwedish"<br />-   ["turkish"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Quaisquer termos com base no dicionário são marcados como palavras-chave, o que evita a lematização da cadeia. Deve ser colocado antes de qualquer filtro de lematização.<br /><br /> **Opções**<br /><br /> rules (tipo: cadeia de caracteres) — regras de stemming rules no seguinte formato "word => stem", por exemplo, "ran => run". O padrão é uma lista vazia.  Obrigatório.|  
|[palavras irrelevantes](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Remove palavras irrelevantes de um fluxo de tokens. Por padrão, o filtro usa uma lista de palavras irrelevantes predefinida para inglês.<br /><br /> **Opções**<br /><br /> stopwords (tipo: matriz de cadeia de caracteres) — uma lista de palavras irrelevantes. Não pode ser especificado se a opção stopwordsList for especificada.<br /><br /> stopwordsList (tipo: matriz de cadeia de caracteres) — uma lista predefinida de palavras irrelevantes. Não pode ser especificado se a opção stopwords for especificada. Entre os valores permitidos estão:"arabic", "armenian", "basque", "brazilian", "bulgarian", "catalan", "czech", "danish", "dutch", "english", "finnish", "french", "galician", "german", "greek", "hindi", "hungarian", "indonesian", "irish", "italian", "latvian", "norwegian", "persian", "portuguese", "romanian", "russian", "sorani", "spanish", "swedish", "thai", "turkish", padrão: "english". Não pode ser especificado se a opção stopwords for especificada. <br /><br /> ignoreCase (tipo: bool) — se true, primeiro todas as palavras são alteradas para minúsculas. O padrão é false.<br /><br /> removeTrailing (tipo: bool) — se true, ignora o último termo de pesquisa se for uma palavra irrelevante. O padrão é true.
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Corresponde sinônimos de palavras simples ou múltiplas em um fluxo de token.<br /><br /> **Opções**<br /><br /> synonyms (tipo: cadeia de caracteres) — obrigatório. Lista de sinônimos em um dos dois formatos a seguir:<br /><br /> -incrível, inacreditável, fabuloso => maravilhoso — todos os termos no lado esquerdo da => são substituídos por todos os termos do lado direito.<br /><br /> -incrível, inacreditável, fabuloso, maravilhoso — uma lista separada por vírgulas de palavras equivalentes. Defina a opção de expansão para alterar como essa lista é interpretada.<br /><br /> ignoreCase (tipo: bool) — entrada e maiúscula/minúscula para correspondência. O padrão é false.<br /><br /> expand (tipo: bool) — se true, todas as palavras na lista de sinônimos (se a notação => não for usada) mapeiam uma a outra. <br />A lista a seguir: incrível, inacreditável, fabuloso, maravilhoso é quivalente a: incrível, inacreditável, fabuloso, maravilhoso => incrível, inacreditável, fabuloso, maravilhoso<br /><br />- Se false, a lista a seguir: incrível, inacreditável, fabuloso, maravilhoso é quivalente a: incrível, inacreditável, fabuloso, maravilhoso => incrível, inacreditável, fabuloso, maravilhoso => incrível.|  
|[cortar](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Corta o espaço em branco à esquerda e à direita de uma cadeia de tokens. |  
|[truncate](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Trunca os termos em um comprimento específico.<br /><br /> **Opções**<br /><br /> length (tipo: int) — padrão: 300, máximo: 300. Obrigatório.|  
|[unique](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtra os tokens com o mesmo texto como o token anterior.<br /><br /> **Opções**<br /><br /> onlyOnSamePosition (tipo: bool) — se definido, remova duplicatas apenas na mesma posição. O padrão é true.|  
|[uppercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(o tipo aplica-se somente quando há opções disponíveis)  |Normaliza o texto do token para letras maiúsculas. |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Divide as palavras em subpalavras e realiza transformações opcionais em grupos de subpalavras.<br /><br /> **Opções**<br /><br /> generateWordParts (tipo: bool) — faz com que partes de palavras sejam geradas, por exemplo "AzureSearch" torna-se "Azure" "Search". O padrão é true.<br /><br /> generateNumberParts (tipo: bool) — faz com que subpalavras de números sejam geradas. O padrão é true.<br /><br /> catenateWords (tipo: bool) — faz com que execuções máximas de partes de palavras sejam unidas, por exemplo "Azure-Search" torna-se "AzureSearch". O padrão é false.<br /><br /> catenateNumbers (tipo: bool) — faz com que execuções máximas de número de blocos sejam unidas, por exemplo "1-2" torna-se "12". O padrão é false.<br /><br /> catenateAll (tipo: bool) — faz com que todas as partes de subpalavras sejam aglutinadas, por exemplo, "Azure-Search-1" torna-se "AzureSearch1". O padrão é false.<br /><br /> splitOnCaseChange (tipo: bool) — se true, divide palavras com base em mudança de maiúscula/minúscula, por exemplo, "AzureSearch" torna-se "Azure" "Search". O padrão é true.<br /><br /> preserveOriginal — faz com que as palavras originais sejam preservadas e adicionadas à lista de subpalavras. O padrão é false.<br /><br /> splitOnNumerics (tipo: bool) — se true, divide com números, por exemplo, "Azure1Search" torna-se "Azure" "1" "Search". O padrão é true.<br /><br /> stemEnglishPossessive (tipo: bool) — faz com que o "s" à direita seja removido para cada subpalavra. O padrão é true.<br /><br /> protectedWords (tipo: cadeia de caracteres) — tokens a proteger contra a delimitação. O padrão é uma lista vazia.|  

 <sup>1</sup> Os tipos de filtro de token são sempre prefixados em código com "#Microsoft.Azure.Search", ou seja, "ArabicNormalizationTokenFilter" na verdade seria "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo em seu código.  

> [!NOTE]
> É necessário que você configure seu analisador personalizado para não produzir tokens com mais de 300 caracteres. A indexação falha para documentos com esses tokens. Para segmentá-los ou ignorá-los, use **TruncateTokenFilter** e **LengthTokenFilter** respectivamente.


## <a name="see-also"></a>Consulte também  
 [REST do Azure Search Service](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analisadores no Azure Search > Exemplos](search-analyzers.md#examples)    
 [Criar índice (API REST do Serviço de Pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index)  
