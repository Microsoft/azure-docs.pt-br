---
title: Analisadores no Azure Search | Microsoft Docs
description: "Atribua analisadores a campos de pesquisa de texto pesquisáveis em um índice para substituir Lucene Standard padrão com alternativas personalizadas específicas a um idioma ou predefinidas."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/11/2017
ms.author: heidist
ms.openlocfilehash: f9e456a57bae4aab25ef85c93132308f2c442c0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyzers-in-azure-search"></a>Analisadores no Azure Search

Um *analisador* é um componente da [pesquisa de texto completa](search-lucene-query-architecture.md), responsável pelo processamento de texto em cadeias de caracteres de consulta e os documentos indexados. Durante a indexação, um analisador transforma o texto em *tokens*, que são gravados como *termos indexados* no índice. Durante a pesquisa, um analisador executa as mesmas transformações em *termos de consulta*, fornecendo a base para a correspondência de termos no índice.

As transformações a seguir são comuns durante a análise:

+ Palavras não essenciais (palavras irrelevantes) e sinais de pontuação são removidos.
+ Frases e palavras hifenizadas são divididos em partes componentes.
+ Palavras de letras maiúsculas estão em minúsculas.
+ As palavras são reduzidas para formas raiz, de modo que uma correspondência pode ser encontrada independentemente da conjugação.

O Azure Search usa o [analisador Lucene Standard](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) como padrão. Você pode substituir o padrão campo por campo. Este artigo descreve a faixa de opções e oferece melhores práticas para a análise personalizada. Ele também fornece exemplos de configurações para os principais cenários.

## <a name="supported-analyzers"></a>Analisadores com suporte

A lista a seguir descreve quais analisadores têm suporte no Azure Search.

| Categoria | Descrição |
|----------|-------------|
| [Analisador Lucene padrão](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Padrão. Nenhuma especificação ou a configuração é necessária. Esse analisador de uso geral funciona bem para a maioria dos idiomas e cenários.|
| Analisadores predefinidos | Oferecidos como um produto acabado se destina a ser usado no estado em que se encontra, com personalização limitada. <br/>Há dois tipos: especializado e de idioma. O que os torna "predefinidos" é que você os referencia por nome, sem nenhuma personalização. <br/><br/>[Analisadores especializados (independentes de idioma)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) para entradas de texto que exigem processamento especializados ou o mínimo de processamento. Analisadores de idioma não predefinidos incluem **Asciifolding**, **Palavra-chave**, **Padrão**, **Simples**, **Interromper**, **Espaço em branco**.<br/><br/>[Analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) são usados quando você precisa de suporte linguístico avançado para idiomas individuais. O Azure Search dá suporte a 35 analisadores de idioma Lucene e 50 analisadores de processamento de idioma natural Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Uma configuração definida pelo usuário de uma combinação de elementos existentes, consistindo de um tokenizador (obrigatório) e filtros opcionais (char ou token).|

Você pode personalizar um analisador predefinido, como **Padrão** ou **Interromper**, para usar opções alternativas documentadas em [Referência do analisador predefinido](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Apenas alguns dos analisadores predefinidos têm opções que você pode definir. Como com qualquer personalização, forneça sua nova configuração com um nome, como *myPatternAnalyzer* para distingui-lo do analisador de Padrões Lucene.

## <a name="how-to-specify-analyzers"></a>Como especificar os analisadores

1. (somente para os analisadores personalizados) Crie uma seção **analyzer** na definição do índice. Para obter mais informações, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [Analisadores Personalizados > Criar](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Em uma [definição de campo](https://docs.microsoft.com/rest/api/searchservice/create-index) no índice, defina a propriedade **analyzer** para o nome de um analisador de destino (por exemplo, `"analyzer" = "keyword"`). Os valores válidos incluem um nome de analisador predefinido, um analisador de idioma ou um analisador personalizado também definido no esquema de índice.

3. Opcionalmente, em vez de uma propriedade **analyzer**, você pode definir diferentes analisadores para indexação e consulta usando os parâmetros de campo **indexAnalyzer** e **searchAnalyzer`**. 

3. Adicionar um analisador a uma definição de campo incorre em uma operação de gravação no índice. Se você adicionar um **analyzer** a um índice existente, observe as seguintes etapas:
 
 | Cenário | Etapas |
 |----------|-------|
 | Adicionar um novo campo | Se o campo ainda não existir no esquema, não haverá nenhuma revisão de campo a ser feita. A análise de texto ocorre sempre que você adiciona ou atualiza os documentos que fornecem conteúdo para o novo campo. Use [Índice de Atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para esta tarefa.|
 | Adicione um analisador a um campo indexado existente. | O índice invertido para esse campo deve ser recriado desde o início do backup e o conteúdo do documento para esses campos deve ser reindexado. <br/> <br/>Para índices em desenvolvimento ativo, [exclua](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [crie](https://docs.microsoft.com/rest/api/searchservice/create-index) o índice para acompanhar a nova definição de campo. <br/> <br/>Para os índices em produção, você deve criar um novo campo para fornecer a definição revisada e começar a usá-lo. Use [Índice de Atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para incorporar o novo campo. Posteriormente, como parte da manutenção planejada do índice, será possível limpar o índice para remover campos obsoletos. |

## <a name="best-practices"></a>Práticas recomendadas

Esta seção oferece sugestões sobre como trabalhar com analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura/gravação, a menos que você tenha requisitos específicos

O Azure Search permite especificar diferentes analisadores para indexação e pesquisa através dos parâmetros de campo adicionais `indexAnalyzer` e `searchAnalyzer`. Se não forem especificados, o analisador definido com a propriedade `analyzer` é usado para indexação e pesquisa. Se `analyzer` não for especificado, o analisador padrão para Lucene Standard será usado.

Uma regra geral é usar o mesmo analisador para indexação e consulta, a menos que requisitos específicos estabeleçam o contrário. Teste cuidadosamente. Quando o processamento de texto for diferente no momento de pesquisa e de indexação, você corre o risco de haver incompatibilidade entre os termos de consulta e os termos de indexação quando as configurações do analisador de pesquisa e de indexação não estiverem alinhadas.

### <a name="test-during-active-development"></a>Testar durante o desenvolvimento ativo

Substituir o analisador padrão requer um rebuild de índice. Se possível, escolha os analisadores a usar durante o desenvolvimento ativo antes de acumular um índice para produção.

### <a name="compare-analyzers-side-by-side"></a>Comparar analisadores lado a lado

É recomendável usar a [API de análise](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). A resposta consiste em tokens, como gerados por um analisador específico para o texto fornecido por você. 

> [!Tip]
> A [demonstração do analisador de pesquisa](http://alice.unearth.ai/) mostra uma comparação lado a lado do analisador Lucene padrão, do analisador de idioma inglês do Lucene e do processador de idioma inglês natural da Microsoft. Para cada entrada de pesquisa fornecida por você, resultados de cada analisador são exibidos nos painéis adjacentes.

## <a name="examples"></a>Exemplos

Os exemplos abaixo mostram definições do analisador para alguns cenários principais.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Exemplo 1: opções personalizadas

Este exemplo ilustra uma definição de analisador com opções personalizadas. Opções personalizadas para filtros de caractere, tokenizadores e filtros de token são especificadas separadamente como constructos nomeados e, depois, referenciadas na definição do analisador. Elementos predefinidos são usados no estado em que se encontram e simplesmente referenciados por nome.

Percorrendo este exemplo:

* Analisadores são uma propriedade da classe do campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Ele pode ser levemente personalizado (por exemplo, personalizando uma única opção em um filtro) ou personalizado em vários locais.
* Nesse caso, o analisador personalizado é "my_analyzer" que, por sua vez, usa um tokenizador padrão personalizado "my_standard_tokenizer" e dois filtros de token: filtro de letras minúsculas e filtro asciifolding personalizado "my_asciifolding".
* Ele também define um filtro de caractere personalizado "map_dash" para substituir todos os traços com sublinhados antes da geração de tokens (o tokenizer padrão faz uma quebra ao encontrar traço, mas não ao encontrar sublinhado).

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
              "map_dash"
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
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
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

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Exemplo 2: substituir o analisador padrão

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

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Exemplo 3: analisadores diferentes para operações de indexação e pesquisa

As APIs incluem atributos de índice adicionais para especificar diferentes analisadores para indexação e pesquisa. Os atributos `searchAnalyzer` e `indexAnalyzer` devem ser especificados como um par, substituindo o atributo `analyzer` único.


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

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Exemplo 4: analisador de linguagem

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

## <a name="next-steps"></a>Próximas etapas

+ Examine nossa explicação abrangente sobre [como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md). Esse artigo usa exemplos para explicar comportamentos que, à primeira vista, podem parecer contraintuitivos.

+ Tente outras sintaxes de consulta a partir da seção de exemplo [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) da [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) no gerenciador de pesquisa no portal.

+ Saiba como aplicar [analisadores léxicos específico do idioma](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configurar analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para o mínimo de processamento ou processamento especializado em campos individuais.

+ [Compare os analisadores padrão e inglês](http://alice.unearth.ai/) em painéis adjacentes neste site da Web de demonstração. 

## <a name="see-also"></a>Consulte também

 [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Controlar os resultados da pesquisa](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
