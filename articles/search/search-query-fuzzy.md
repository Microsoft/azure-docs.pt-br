---
title: Pesquisa difusa
titleSuffix: Azure Cognitive Search
description: Implemente uma experiência de pesquisa "você quis dizer" para corrigir automaticamente erros de grafia ou de digitação.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b2f2c8497d5365104a5ffc361b791450925d0c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694777"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Pesquisa difusa para corrigir erros ortográficos e erros de digitação

O Azure Cognitive Search dá suporte para a pesquisa difusa, um tipo de consulta que releva erros de digitação e de grafia na cadeia de caracteres de entrada. Para isso, ela examina os termos que têm uma composição semelhante. A expansão da pesquisa para abranger correspondências próximas tem o efeito de corrigir automaticamente erros de grafia quando a discrepância é de apenas alguns caracteres incorretos. 

## <a name="what-is-fuzzy-search"></a>O que é a pesquisa difusa?

É um exercício de expansão que produz uma correspondência em termos que têm uma composição semelhante. Quando uma pesquisa difusa é especificada, o mecanismo cria um grafo (com base na [teoria de automação finita determinística](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) de termos com composição semelhante, para todos os termos na consulta. Por exemplo, se a consulta incluir os três termos "Universidade de Washington", um grafo será criado para cada termo na consulta `search=university~ of~ washington~` (não há remoção de palavras irrelevantes na pesquisa difusa, portanto, é criado um grafo para "de").

O grafo consiste em até 50 expansões, ou permutas, de cada termo, capturando variantes corretas e incorretas no processo. Em seguida, o mecanismo retorna as correspondências mais altas relevantes na resposta. 

Para um termo como "universidade", o grafo pode conter "unversidade, universdade, univercidade, universo, inverso". Todos os documentos que correspondem àqueles no grafo são incluídos nos resultados. Em contraste com outras consultas que analisam o texto para lidar com diferentes formas da mesma palavra ("rato" e "ratos"), as comparações em uma consulta difusa são consideradas literalmente, sem análise linguística no texto. "Universo" e "inverso", que são semanticamente diferentes, serão correspondentes, pois as discrepâncias sintáticas são pequenas.

Uma correspondência será realizada com sucesso se as discrepâncias forem limitadas a duas ou menos edições, que correspondem a inserção, exclusão, substituição ou transposição de caracteres. O algoritmo de correção de cadeia de caracteres que especifica o diferencial é a métrica de [distância de Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance), que é o "número mínimo de operações (inserções, exclusões, substituições ou transposições de dois caracteres adjacentes) necessário para converter uma palavra em outra". 

No Azure Cognitive Search:

+ A consulta difusa se aplica a termos inteiros, mas você pode usar frases por meio de construções AND. Por exemplo, "Universidade~ de~ "Wshington~" geraria correspondência com "Universidade de Washington".

+ A distância padrão de uma edição é 2. O valor `~0` significa sem expansão (somente o termo exato é considerado uma correspondência), mas você pode especificar `~1` para um grau de diferença ou uma edição. 

+ Uma consulta difusa pode expandir um termo até 50 permutações adicionais. Esse limite não é configurável, mas você pode reduzir efetivamente o número de expansões diminuindo a distância de edição para 1.

+ As respostas consistem em documentos que contêm uma correspondência relevante (até 50).

Coletivamente, os grafos são enviados como critérios de correspondência em relação aos tokens no índice. Como você pode imaginar, a pesquisa difusa é inerentemente mais lenta do que outras formas de consulta. O tamanho e a complexidade do índice podem determinar se os benefícios são suficientes para compensar a latência da resposta.

> [!NOTE]
> Como a pesquisa difusa tende a ser lenta, pode valer a pena investigar alternativas como a indexação de n-gram, com sua progressão de sequências de caracteres curtas (duas e três sequências de caracteres para tokens bigramas e trigramas). Dependendo da linguagem e da superfície de consulta, o n-gram pode ter melhor desempenho. A desvantagem é que a indexação de n-gram exige muito armazenamento e gera índices muito maiores.
>
> Outra alternativa, que você pode considerar se deseja lidar apenas com os principais casos, seria um [mapa de sinônimos](search-synonyms.md). Por exemplo, mapear "pesquisa" a "pesqiusa, pesuisa, psquisa" ou "recuperar" a "recuprar".

## <a name="indexing-for-fuzzy-search"></a>Indexação para pesquisa difusa

Os analisadores não são usados durante o processamento da consulta para criar um grafo de expansão, mas isso não significa que os analisadores devem ser ignorados em cenários de pesquisa difusa. Afinal de contas, os analisadores são usados durante a indexação para criar tokens nos quais a correspondência é feita, quer a consulta seja de texto livre, pesquisa filtrada ou pesquisa difusa com um grafo como entrada. 

Geralmente, ao atribuir analisadores para cada campo, a decisão de ajustar a cadeia de análise é baseada no caso de uso primário (um filtro ou uma pesquisa de texto completa) em vez de formulários de consulta especializados, como na pesquisa difusa. Por esse motivo, não há uma recomendação de analisador específica para a pesquisa difusa. 

No entanto, se as consultas de teste não produzirem as correspondências esperadas, tente variar o analisador de indexação, definindo-o como um [analisador de linguagem](index-add-language-analyzers.md), para ver se você obtém resultados melhores. Em alguns idiomas, especialmente aqueles com mutações de vogal, pode ser útil usar as formas flexionadas e irregulares de palavras geradas pelos processadores de linguagem natural da Microsoft. Em alguns casos, o uso do analisador de linguagem correto pode fazer diferença na indexação do termo de maneira que seja compatível com o valor fornecido pelo usuário.

## <a name="how-to-use-fuzzy-search"></a>Como usar a pesquisa difusa

As consultas difusas são criadas usando a sintaxe de consulta Lucene completa, invocando o [analisador de consulta Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Defina o analisador Lucene completo na consulta (`queryType=full`).

1. Caso queira, delimite a solicitação a campos específicos usando esse parâmetro (`searchFields=<field1,field2>`). 

1. Acrescente o operador til (`~`) ao final do termo inteiro (`search=<string>~`).

   Inclua um parâmetro opcional, um número entre 0 e 2 (padrão), se você quiser especificar a distância de edição (`~1`). Por exemplo, "mar~" ou "mar~1" retornaria "mar", "amar" e "maré".

No Azure Cognitive Search, além do termo e da distância (máximo de 2), não há outros parâmetros para definir na consulta.

> [!NOTE]
> Durante o processamento, as consultas difusas não passam por [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis). A entrada da consulta é adicionada diretamente à árvore de consulta e expandida para criar um grafo de termos. A única transformação executada é a mudança das letras maiúsculas para minúsculas.

## <a name="testing-fuzzy-search"></a>Teste da pesquisa difusa

Para testes simples, recomendamos o [Gerenciador de pesquisa](search-explorer.md) ou o [Postman](search-get-started-rest.md) para iterar uma expressão de consulta. Ambas as ferramentas são interativas, o que significa que você pode percorrer rapidamente várias variantes de um termo e avaliar as respostas que retornam.

Quando os resultados são ambíguos, o [realce de ocorrências](search-pagination-page-layout.md#hit-highlighting) ajuda a identificar a correspondência na resposta. 

> [!Note]
> O uso do realce de ocorrências para identificar correspondências difusas tem limitações e funciona apenas para pesquisa difusa básica. Se o índice tiver perfis de pontuação ou você adicionar camadas à consulta com sintaxe adicional, talvez o realce de ocorrências não identifique a correspondência. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Exemplo 1: pesquisa difusa com o termo exato

Suponha que a seguinte cadeia de caracteres exista no campo `"Description"` em um documento de pesquisa: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Comece com uma pesquisa difusa por "especial" e adicione o realce de ocorrências ao campo Descrição:

```console
search=special~&highlight=Description
```

Na resposta, como você adicionou o realce de ocorrências, a formatação é aplicada a "especial" como o termo correspondente.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Faça a solicitação novamente, digitando incorretamente "especial" com a remoção de algumas letras ("pe"):

```console
search=scial~&highlight=Description
```

Até agora, nenhuma alteração na resposta. Usando o padrão de dois graus de distância, a remoção dos dois caracteres "pe" de "especial" ainda permite uma correspondência com o termo.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Tentando mais uma solicitação, modifique ainda mais o termo de pesquisa, retirando um último caractere para um total de três exclusões (de "especial" para "esial"):

```console
search=scal~&highlight=Description
```

Observe que a mesma resposta é retornada, mas agora, em vez de corresponder a "especial", a correspondência difusa é "SQL".

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

O propósito desse exemplo expandido é ilustrar a clareza que o realce de ocorrências pode trazer a resultados ambíguos. Em todos os casos, o mesmo documento é retornado. Se você usasse IDs de documento para verificar uma correspondência, talvez tivesse ignorado a mudança de "especial" para "SQL".

## <a name="see-also"></a>Confira também

+ [Como a pesquisa de texto completo funciona no Azure Cognitive Search (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](./search-get-started-dotnet.md)
+ [Como consultar em REST](./search-get-started-powershell.md)