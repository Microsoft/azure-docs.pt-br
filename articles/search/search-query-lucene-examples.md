---
title: Exemplos de consulta do Lucene – Azure Search
description: Sintaxe de consulta Lucene para pesquisa difusa, pesquisa por proximidade, aumento de termos, pesquisa com expressão regular e pesquisas com curinga em um serviço do Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: affe9084c488984747c4bafca5b8e9536cd6dba8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485414"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Exemplos de consulta usando a sintaxe de pesquisa "full" Lucene (consultas avançadas no Azure Search)

Ao construir consultas para o Azure Search, você pode substituir o padrão [analisador de consulta simples](query-simple-syntax.md) com mais expansiva [Lucene Query Parser no Azure Search](query-lucene-syntax.md) para formular a consulta especializada e avançada definições. 

O analisador Lucene dá suporte a construções de consulta complexa, como consultas com escopo de campo, difusas e pesquisa de curinga de prefixo, pesquisa por proximidade, aumento de termos e pesquisa de expressão regular. A energia adicional vem com requisitos adicionais de processamento, portanto, você deve esperar um tempo de execução um pouco mais longo. Neste artigo, execute exemplos em etapas, que demonstram as operações de consulta disponíveis ao usar a sintaxe completa.

> [!Note]
> Muitas construções de consulta especializadas habilitadas por meio da sintaxe de consulta Lucene completa não são [texto analisado](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente caso espera-se a lematização. A análise lexical é realizada somente em termos completos (uma consulta de termo ou de frase). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas. 
>

## <a name="formulate-requests-in-postman"></a>Formular solicitações em Postman

Os exemplos a seguir utilizam um índice de pesquisa de Trabalhos de Nova Iorque que consiste em trabalhos disponíveis com base em um conjunto de dados fornecido pelo [OpenData da cidade de Nova Iorque](https://opendata.cityofnewyork.us/). Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft, o que significa que não é necessária uma assinatura do Azure ou Azure Search para experimentar essas consultas.

O que é necessário é o Postman ou uma ferramenta equivalente para emitir solicitação HTTP em GET. Para obter mais informações, consulte [explorar com clientes REST](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Definir o cabeçalho de solicitação

1. No cabeçalho de solicitação, defina **Content-Type** para `application/json`.

2. Adicione uma**api-key** e defina-a para essa cadeia de caracteres: `252044BE3886FE4A8E3BAA4F595114BB`. Essa é uma chave de consulta para o serviço de pesquisa de área restrita que hospeda o índice Trabalhos de Nova Iorque.

Após especificar o cabeçalho de solicitação, você poderá reutilizá-lo para todas as consultas neste artigo, trocando apenas a cadeia de caracteres **search=** . 

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Definir a URL da solicitação

A solicitação é um comando GET emparelhado com uma URL que contém a cadeia de caracteres de pesquisa e o ponto de extremidade do Azure Search.

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

A composição de URL possui os elementos a seguir:

+ **`https://azs-playground.search.windows.net/`** é um serviço de pesquisa de área restrita mantido pela equipe de desenvolvimento do Azure Search. 
+ **`indexes/nycjobs/`** é o índice Trabalhos de Nova Iorque na coleção de índices desse serviço. O nome do serviço e índice são obrigatórios na solicitação.
+ **`docs`** é a coleção de documentos que contém todo o conteúdo pesquisável. A chave de API de consulta fornecida no cabeçalho da solicitação somente funciona em operações de leitura direcionando a coleção de documentos.
+ **`api-version=2019-05-06`** define a versão da API, que é um parâmetro obrigatório em todas as solicitações.
+ **`search=*`** é a cadeia de caracteres de consulta, que na consulta inicial é nula, retornando os primeiros 50 resultados (por padrão).

## <a name="send-your-first-query"></a>Enviar a primeira consulta

Como uma etapa de verificação, cole a solicitação a seguir no GET e clique em **Enviar**. Os resultados são retornados como documentos JSON detalhados. Documentos inteiros são retornados, que permite que você veja todos os campos e todos os valores.

Cole essa URL em um cliente REST como uma etapa de validação e exibir a estrutura do documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

A cadeia de caracteres de consulta, **`search=*`** , é uma pesquisa não especificada equivalente à pesquisa nula ou vazia. É a pesquisa mais simples, que você pode fazer.

Opcionalmente, é possível adicionar **`$count=true`** à URL para retornar uma contagem dos documentos que correspondem aos critérios de pesquisa. Em uma cadeia de pesquisa vazia, esses são todos os documentos no índice (cerca de 2800 no caso de NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise completa do Lucene

Adicione **queryType=full** para invocar a sintaxe de consulta completa, substituindo a sintaxe de consulta simples padrão. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Todos os exemplos deste artigo especificam o parâmetro de pesquisa **queryType=full**, indicando que a sintaxe completa é tratada pelo Analisador de Consulta Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemplo 1: Consulta com escopo para uma lista de campos

Este primeiro exemplo não é específico do Lucene, mas podemos levar com ele para introduzir o conceito de consulta fundamentais primeiro: escopo de campo. Este exemplo abrange toda a consulta e a resposta para apenas alguns campos específicos. Saber como estruturar uma resposta JSON legível é importante quando sua ferramenta é Postman ou Search Explorer. 

Por uma questão de brevidade, a consulta direciona apenas o campo *business_title* e especifica que somente os títulos da empresa sejam retornados. O **searchFields** parâmetro restringe a execução de consulta apenas o campo business_title, e **selecione** Especifica quais campos serão incluídos na resposta.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
&search=*&searchFields=business_title&$select=business_title
```

Aqui está a mesma consulta com vários campos em uma lista delimitada por vírgulas.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Os espaços após as vírgulas são opcionais.

> [!Tip]
> Ao usar a API REST do seu código do aplicativo, não se esqueça de parâmetros de codificação de URL, como `$select` e `searchFields`.

### <a name="full-url"></a>URL completa

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  ![Resposta de exemplo do Postman](media/search-query-lucene-examples/postman-sample-results.png)

Você deve ter notado a pontuação de pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há classificação, seja porque a pesquisa não foi pesquisa de texto completo ou porque nenhum critério foi aplicado. Para pesquisa nula sem critérios, as linhas retornam em ordem arbitrária. Quando você incluir critérios de pesquisa real, você verá as pontuações de evoluem em valores significativos de pesquisa.

## <a name="example-2-fielded-search"></a>Exemplo 2: Pesquisa por campo

Sintaxe completa do Lucene dá suporte a expressões de pesquisa individual de escopo a um campo específico. Este exemplo pesquisa cargos executivos com o termo sênior neles, mas não júnior.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Aqui está a mesma consulta com vários campos.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>URL completa

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Resposta de exemplo do Postman](media/search-query-lucene-examples/intrafieldfilter.png)

Você pode definir uma operação de pesquisa por campo com o **fieldName:searchExpression** sintaxe, em que a expressão de pesquisa pode ser uma única palavra ou uma frase ou uma expressão mais complexa entre parênteses, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Certifique-se de colocar várias cadeias de caracteres entre aspas, se você quiser que as duas cadeias de caracteres a ser avaliada como uma única entidade, como nesse caso, procurando por dois locais distintos no `state` campo. Além disso, verifique se o operador está em maiúsculas, como você pode ver com NÃO e E.

O campo especificado na **fieldName:searchExpression** deve ser um campo pesquisável. Confira [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Criar índice [API REST do Serviço Azure Search]) para obter detalhes sobre como os atributos de índice são usados em definições de campo.

> [!NOTE]
> No exemplo acima, não precisamos usar o `searchFields` parâmetro porque cada parte da consulta tem um nome de campo especificado explicitamente. No entanto, você ainda pode usar o `searchFields` parâmetro se você quiser executar uma consulta em que algumas partes limitam-se a um campo específico, e o restante pode aplicar a vários campos. Por exemplo, a consulta `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` corresponderia `senior NOT junior` somente à `business_title` campo, enquanto ela corresponderia a "external" com o `posting_type` campo. O nome do campo fornecido na **fieldName:searchExpression** sempre tem precedência sobre o `searchFields` parâmetro, que é por isso que neste exemplo, não precisamos incluir `business_title` no `searchFields` parâmetro.

## <a name="example-3-fuzzy-search"></a>Exemplo 3: Pesquisa difusa

A sintaxe Lucene completa também dá suporte à pesquisa difusa, combinando em termos que têm uma construção semelhante. Para fazer uma pesquisa difusa, acrescente o símbolo til `~` ao final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` retornariam mar, amar e maré.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Expressões não têm suporte diretamente, mas você pode especificar uma correspondência difusa em partes componentes de uma frase.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>URL completa

Essa consulta pesquisa trabalhos com o termo "associado" (deliberadamente com ortografia incorreta):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Resposta de pesquisa difusa](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> As consultas difusas não são [analisadas](search-lucene-query-architecture.md#stage-2-lexical-analysis). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: Pesquisa por proximidade
As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, "hotel aeroporto"~5 encontrará os termos hotel e aeroporto em cinco palavras uma da outra em um documento.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>URL completa

Nessa consulta, para trabalhos com o termo "analista sênior", em que é separado por até uma palavra:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Consulta de proximidade](media/search-query-lucene-examples/proximity-before.png)

Tente novamente removendo as palavras entre o termo "analista sênior". Observe que 8 documentos são retornados para essa consulta, em oposição a 10 para a consulta anterior.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exemplo 5: Aumento de termos
O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Para aumentar um termo, use o sinal de interpolação, "^", com um fator de aumento (um número) no final do termo que você está pesquisando. 

### <a name="full-urls"></a>URLs completas

Nessa consulta para "antes", pesquise trabalhos com o termo *analista de computador* e observe que não há resultados com as palavras *computador* e *analista*, mas os trabalhos de *computador* estão no topo dos resultados.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Aumento antes de termos](media/search-query-lucene-examples/termboostingbefore.png)

Na consulta para "depois" repita a pesquisa, dessa vez, aumentando os resultados com o termo *analista* em relação ao termo *computador*, caso ambas as palavras não existam. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Uma versão em formato mais legível por humanos da consulta acima é `search=business_title:computer analyst^2`. Para uma consulta que funcione, `^2` é codificado como `%5E2`, o que é mais difícil de ver.

  ![Aumento depois de termos](media/search-query-lucene-examples/termboostingafter.png)

O aumento de termos difere dos perfis de pontuação, em que os perfis de pontuação aumentam determinados campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como **gênero** no exemplo de índice de loja de música. O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, "rock^2 eletrônico" melhorará a posição dos documentos que contêm os termos de pesquisa no campo **gênero** , à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa "rock" serão mais bem classificados do que o outro termo de pesquisa "eletrônico" como resultado do valor de aumento de termo (2).

Ao definir o nível do fator, quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,2).


## <a name="example-6-regex"></a>Exemplo 6: Regex

Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>URL completa

Nesta consulta, procure trabalhos com o termo sênior ou júnior: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Consulta de regex](media/search-query-lucene-examples/regex.png)

> [!Note]
> Consultas Regex não são [analisadas](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: Pesquisa com curinga
Você pode usar a sintaxe geralmente reconhecida para pesquisas com vários caracteres curinga (\*) ou um caractere curinga (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>URL completa

Nessa consulta, pesquise trabalhos que contenham o prefixo 'prog', que incluiria títulos de negócios com os termos programação e programador. Não é possível usar um símbolo * ou ? como o primeiro caractere de uma pesquisa.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Consulta de caractere curinga](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Consultas com curingas não são [analisadas](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas.
>

## <a name="next-steps"></a>Próximas etapas
Tente especificar o Analisador de Consulta Lucene em seu código. Os links a seguir explicam como configurar consultas de pesquisa para o .NET e para a API REST. Os links usam a sintaxe simples padrão e, portanto, será necessário aplicar o que você aprendeu neste artigo para especificar o **queryType**.

* [Consultar seu índice do Azure Search usando o SDK do .NET](search-query-dotnet.md)
* [Consultar seu índice do Azure Search usando a API REST](search-create-index-rest-api.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos links a seguir:

+ [Exemplos de consulta de sintaxe simples](search-query-simple-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)