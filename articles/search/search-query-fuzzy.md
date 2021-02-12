---
title: Pesquisa difusa
titleSuffix: Azure Cognitive Search
description: Implemente uma experiência de pesquisa "você quis dizer" para corrigir automaticamente um erro ou grafia incorreta.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: ac2690a5f18bb58c29b433f4a07e52096bbd268b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701578"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Pesquisa difusa para corrigir erros ortográficos e erros de digitação

O Azure Pesquisa Cognitiva dá suporte à pesquisa difusa, um tipo de consulta que compensa erros de digitação e os termos incorretos na cadeia de caracteres de entrada. Ele faz isso examinando os termos que têm uma composição semelhante. Expandir a pesquisa para abranger quase as correspondências tem o efeito de corrigir automaticamente um erros de grafia quando a discrepância é apenas alguns caracteres incorretos. 

## <a name="what-is-fuzzy-search"></a>O que é pesquisa difusa?

É um exercício de expansão que produz uma correspondência em termos que têm uma composição semelhante. Quando uma pesquisa difusa é especificada, o mecanismo cria um grafo (com base na [teoria de automação finita de determinístico](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) de termos compostos de forma semelhante, para todos os termos na consulta. Por exemplo, se sua consulta incluir três termos "Universidade de Washington", um grafo será criado para cada termo na consulta `search=university~ of~ washington~` (não há nenhuma remoção de palavra STOP na pesquisa difusa, portanto "de" Obtém um grafo).

O grafo consiste em até 50 expansões, ou permutas, de cada termo, capturando variantes corretas e incorretas no processo. Em seguida, o mecanismo retorna as correspondências mais altas relevantes na resposta. 

Para um termo como "University", o grafo pode ter "unversty, Universty, Universidade, universo, inverso". Todos os documentos que correspondem a aqueles no grafo são incluídos nos resultados. Em contraste com outras consultas que analisam o texto para lidar com diferentes formas da mesma palavra ("mouse" e "mouse"), as comparações em uma consulta difusa são obtidas com o valor facial sem nenhuma análise linguística no texto. "Universo" e "inverso", que são semanticamente diferentes, serão correspondentes, pois as discrepâncias sintáticas são pequenas.

Uma correspondência será realizada com sucesso se as discrepâncias forem limitadas a duas ou menos edições, onde uma edição é um caractere inserido, excluído, substituído ou transposedo. O algoritmo de correção de cadeia de caracteres que especifica o diferencial é a métrica de [distância Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) , descrita como o "número mínimo de operações (inserções, exclusões, substituições ou transposições de dois caracteres adjacentes) necessário para alterar uma palavra para a outra". 

No Azure Pesquisa Cognitiva:

+ A consulta difusa se aplica a termos inteiros, mas você pode dar suporte a frases e construções. Por exemplo, "Unviersty ~ de ~" Wshington ~ "corresponderia em" University de Washington ".

+ A distância padrão de uma edição é 2. Um valor de `~0` significa sem expansão (somente o termo exato é considerado uma correspondência), mas você pode especificar `~1` para um grau de diferença ou uma edição. 

+ Uma consulta difusa pode expandir um termo até 50 permutações adicionais. Esse limite não é configurável, mas você pode reduzir efetivamente o número de expansões, diminuindo a distância de edição para 1.

+ As respostas consistem em documentos que contêm uma correspondência relevante (até 50).

Coletivamente, os grafos são enviados como critérios de correspondência em relação aos tokens no índice. Como você pode imaginar, a pesquisa difusa é inerentemente mais lenta do que outros formulários de consulta. O tamanho e a complexidade do índice podem determinar se os benefícios são suficientes para deslocar a latência da resposta.

> [!NOTE]
> Como a pesquisa difusa tende a ser lenta, pode valer a pena investigar alternativas como a indexação de n-Gram, com sua progressão de sequências de caracteres curtas (duas e três sequências de caracteres para tokens bigram e Trigram). Dependendo da sua linguagem e da superfície de consulta, o n-Gram pode fornecer um melhor desempenho. A compensação é que a indexação de n-Gram é muito intensiva no armazenamento e gera índices muito maiores.
>
> Outra alternativa, que você pode considerar se deseja lidar apenas com os casos mais flagrantes, seria um mapa de [sinônimos](search-synonyms.md). Por exemplo, mapear "Search" para "serach, serch, sarch" ou "retrieve" para "recuperar".

## <a name="indexing-for-fuzzy-search"></a>Indexação para pesquisa difusa

Os analisadores não são usados durante o processamento da consulta para criar um grafo de expansão, mas isso não significa que os analisadores devem ser ignorados em cenários de pesquisa difusa. Afinal de tarefas, os analisadores são usados durante a indexação para criar tokens nos quais a correspondência é feita, quer a consulta seja forma livre, pesquisa filtrada ou uma pesquisa difusa com um grafo como entrada. 

Geralmente, ao atribuir analisadores em uma base por campo, a decisão de ajustar a cadeia de análise é baseada no caso de uso primário (um filtro ou uma pesquisa de texto completo) em vez de formulários de consulta especializados, como pesquisa difusa. Por esse motivo, não há uma recomendação de analisador específica para pesquisa difusa. 

No entanto, se as consultas de teste não estiverem produzindo as correspondências esperadas, você poderá tentar variar o analisador de indexação, definindo-a como um [analisador de linguagem](index-add-language-analyzers.md), para ver se você obtém resultados melhores. Algumas linguagens, especialmente aquelas com mutações de vogal, podem se beneficiar dos formulários de palavras inflexos e irregulares gerados pelos processadores de idioma natural da Microsoft. Em alguns casos, usar o analisador de idioma correto pode fazer uma diferença em se um termo é indexado de forma que seja compatível com o valor fornecido pelo usuário.

## <a name="how-to-use-fuzzy-search"></a>Como usar a pesquisa difusa

As consultas difusas são construídas usando a sintaxe de consulta Lucene completa, invocando o [analisador de consulta Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Defina o analisador Lucene completo na consulta ( `queryType=full` ).

1. Opcionalmente, delimitar a solicitação a campos específicos, usando esse parâmetro ( `searchFields=<field1,field2>` ). 

1. Acrescente o operador til ( `~` ) ao final do termo inteiro ( `search=<string>~` ).

   Inclua um parâmetro opcional, um número entre 0 e 2 (padrão), se você quiser especificar a distância de edição ( `~1` ). Por exemplo, "mar~" ou "mar~1" retornaria "mar", "amar" e "maré".

No Azure Pesquisa Cognitiva, além do termo e da distância (máximo de 2), não há parâmetros adicionais a serem definidos na consulta.

> [!NOTE]
> Durante o processamento da consulta, as consultas difusas não passam por [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis). A entrada da consulta é adicionada diretamente à árvore de consulta e expandida para criar um grafo de termos. A única transformação executada é de letras maiúsculas e minúsculas.

## <a name="testing-fuzzy-search"></a>Testando pesquisa difusa

Para testes simples, recomendamos que o [Search Explorer](search-explorer.md) ou o [postmaster](search-get-started-rest.md) se sobreiterando em uma expressão de consulta. Ambas as ferramentas são interativas, o que significa que você pode percorrer rapidamente várias variantes de um termo e avaliar as respostas que retornam.

Quando os resultados são ambíguos, o [realce de ocorrências](search-pagination-page-layout.md#hit-highlighting) pode ajudá-lo a identificar a correspondência na resposta. 

> [!Note]
> O uso de realce de ocorrências para identificar correspondências difusas tem limitações e funciona apenas para pesquisa difusa básica. Se o índice tiver perfis de pontuação ou se você camadar a consulta com sintaxe adicional, o realce de ocorrências poderá falhar ao identificar a correspondência. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Exemplo 1: pesquisa difusa com o termo exato

Suponha que a cadeia de caracteres a seguir exista em um `"Description"` campo em um documento de pesquisa: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Comece com uma pesquisa difusa em "especial" e adicione o realce de clique ao campo Descrição:

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

Tente a solicitação novamente, digitando incorretamente "especial" por meio de várias letras ("PE"):

```console
search=scial~&highlight=Description
```

Até agora, nenhuma alteração na resposta. Usando o padrão de 2 graus de distância, remover dois caracteres "PE" de "especial" ainda permite uma correspondência bem-sucedida nesse termo.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Tentando mais uma solicitação, modifique ainda mais o termo de pesquisa, retirando um último caractere para um total de três exclusões (de "especial" para "Scala"):

```console
search=scal~&highlight=Description
```

Observe que a mesma resposta é retornada, mas agora, em vez de corresponder ao "especial", a correspondência difusa é "SQL".

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

O ponto deste exemplo expandido é ilustrar a clareza de que o realce de pressionamento pode trazer resultados ambíguos. Em todos os casos, o mesmo documento é retornado. Se você confiava em IDs de documento para verificar uma correspondência, talvez tenha perdido a mudança de "especial" para "SQL".

## <a name="see-also"></a>Veja também

+ [Como a pesquisa de texto completo funciona no Azure Pesquisa Cognitiva (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](./search-get-started-dotnet.md)
+ [Como consultar em REST](./search-get-started-powershell.md)