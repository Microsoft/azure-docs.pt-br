---
title: Sintaxe de consulta Lucene
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta Lucene completa, conforme usada no Azure Pesquisa Cognitiva para curinga, pesquisa difusa, RegEx e outras construções de consulta avançadas.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: fc3662d8198e6ab6ab215ac1e9e8eac585f4250b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801580"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta Lucene no Azure Pesquisa Cognitiva

Ao criar consultas, você pode optar pela sintaxe do [analisador de consulta do Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) para formulários de consulta especializados: curinga, pesquisa difusa, pesquisa por proximidade, expressões regulares. Grande parte da sintaxe do analisador de consulta Lucene é [implementada intacta no Azure pesquisa cognitiva](search-lucene-query-architecture.md), com exceção das *pesquisas de intervalo* que são construídas por meio de **`$filter`** expressões. 

Para usar a sintaxe Lucene completa, você definirá o QueryType como "Full" e passará uma expressão de consulta padronizada para curinga, pesquisa difusa ou um dos outros formulários de consulta com suporte na sintaxe completa. Em repouso, as expressões de consulta são fornecidas no **`search`** parâmetro de uma solicitação de [documentos de pesquisa (API REST)](/rest/api/searchservice/search-documents) .

## <a name="example-full-syntax"></a>Exemplo (sintaxe completa)

O exemplo a seguir é uma solicitação de pesquisa construída usando a sintaxe completa. Este exemplo específico mostra a pesquisa em campo e o aumento de termos. Ele procura Hotéis em que o campo categoria contém o termo "orçamento". Todos os documentos que contêm a frase "recentemente renovados" são classificados como um resultado do valor de aumento de termo (3).  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

Embora não seja específico para qualquer tipo de consulta, o **`searchMode`** parâmetro é relevante neste exemplo. Sempre que os operadores estiverem na consulta, geralmente você deve definir `searchMode=all` para garantir que *todos* os critérios sejam correspondidos.  

Para obter exemplos adicionais, consulte [exemplos de sintaxe de consulta Lucene](search-query-lucene-examples.md). Para obter detalhes sobre a solicitação de consulta e os parâmetros, incluindo searchmode, consulte [Pesquisar documentos (API REST)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Conceitos básicos da sintaxe  

Os conceitos básicos de sintaxe a seguir se aplicam a todas as consultas que usam a sintaxe do Lucene.  

### <a name="operator-evaluation-in-context"></a>Avaliação de operador no contexto

O posicionamento determina se um símbolo é interpretado como um operador ou apenas outro caractere em uma cadeia de caracteres.

Por exemplo, na sintaxe completa do Lucene, o til (~) é usado na busca difusa e na pesquisa de proximidade. Quando colocado depois de uma frase entre aspas, invoca a procura por proximidade. Quando colocado no final de um termo, o ~ invoca a pesquisa difusa.

Dentro de um termo, como "analista~de~negócios", o caractere não é avaliado como um operador. Nesse caso, supondo que a consulta seja um termo ou uma consulta de frase, a [pesquisa de texto completo](search-lucene-query-architecture.md) com [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis) retira o ~ e divide o termo "analista~de~negócios" em dois: analista OU negócios.

O exemplo acima usa o til (~), mas o mesmo princípio se aplica a todos os operadores.

### <a name="escaping-special-characters"></a>Caracteres especiais de escape

Para usar qualquer um dos operadores de pesquisa como parte do texto de pesquisa, escape o caractere prefixando-o com uma barra invertida única ( `\` ). Por exemplo, para uma pesquisa de curinga em `https://` , em que `://` é parte da cadeia de caracteres de consulta, você especificaria `search=https\:\/\/*` . Da mesma forma, um padrão de número de telefone de escape pode ser assim `\+1 \(800\) 642\-7676` .

Os caracteres especiais que exigem escape incluem o seguinte:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Embora o escape Mantenha os tokens juntos, a [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante a indexação pode distribuí-los. Por exemplo, o analisador Lucene padrão irá quebrar palavras em hifens, espaços em branco e outros caracteres. Se você precisar de caracteres especiais na cadeia de caracteres de consulta, talvez seja necessário um analisador que os preserve no índice. Algumas opções incluem [analisadores de idioma](index-add-language-analyzers.md)natural da Microsoft, que preservam palavras hifenizadas ou um analisador personalizado para padrões mais complexos. Para obter mais informações, consulte [termos parciais, padrões e caracteres especiais](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres reservados e não seguros em URLs

Verifique se todos os caracteres reservados e não seguros estão codificados em uma URL. Por exemplo, ' # ' é um caractere não seguro porque é um identificador de fragmento/âncora em uma URL. O caractere deverá ser codificado como `%23` se for usado em uma URL. ' & ' e ' = ' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva do Azure. Consulte [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

Os caracteres não seguros são ``" ` < > # % { } | \ ^ ~ [ ]``. Os caracteres reservados são `; / ? : @ = + &`.

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Operadores boolianos

Você pode inserir operadores boolianos em uma cadeia de caracteres de consulta para melhorar a precisão de uma correspondência. A sintaxe completa dá suporte a operadores de texto, além de operadores de caracteres. Sempre especifique operadores boolianos de texto (AND, OR, NOT) com tudo em maiúsculas.

|Operador de texto | Caractere | Exemplo | Uso |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | Especifica os termos que uma correspondência deve conter. No exemplo, o mecanismo de consulta procurará documentos que contenham `wifi` e `luxury` . O caractere de adição ( `+` ) é usado para os termos necessários. Por exemplo, `+wifi +luxury` estipula que os dois termos devem aparecer em algum lugar no campo de um único documento.|
| OU | `|` | `wifi | luxury` | Localiza uma correspondência quando um dos termos é encontrado. No exemplo, o mecanismo de consulta retornará a correspondência em documentos que contenham um `wifi` ou `luxury` ambos. Porque OR é o operador de conjunção padrão, que também pode ser deixado de fora, de modo que `wifi luxury` é o equivalente de `wifi | luxury`.|
| NOT | `!`, `-` | `wifi –luxury` | Retorna correspondências em documentos que excluem o termo. Por exemplo, o `wifi –luxury` pesquisará documentos que têm o `wifi` termo, mas não `luxury` . <br/><br/>O `searchMode` parâmetro em uma solicitação de consulta controla se um termo com o operador NOT é ANDed ou orns com outros termos na consulta (supondo que não haja nenhum `+` `|` operador OR nos outros termos). Os valores válidos incluem `any` ou `all`.  <br/><br/>`searchMode=any` aumenta a recall de consultas, incluindo mais resultados e, por padrão, `-` será interpretado como "ou não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` ou aqueles que não contêm o termo `luxury`.  <br/><br/>`searchMode=all` aumenta a precisão das consultas, incluindo menos resultados e, por padrão, será interpretado como "e não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo "luxo". Esse é indiscutivelmente um comportamento mais intuitivo para o operador `-`. Portanto, você deve considerar `searchMode=all` o uso do em vez de `searchMode=any` se deseja otimizar as pesquisas de precisão em vez de recall, *e* os usuários frequentemente usam o `-` operador em pesquisas.<br/><br/>Ao decidir sobre uma `searchMode` configuração, considere os padrões de interação do usuário para consultas em vários aplicativos. Os usuários que estão pesquisando informações têm mais probabilidade de incluir um operador em uma consulta, em oposição aos sites de comércio eletrônico que têm estruturas de navegação mais internas. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Pesquisa em campo

Você pode definir uma operação de pesquisa em campo com a `fieldName:searchExpression` sintaxe, em que a expressão de pesquisa pode ser uma única palavra ou frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:  

- gênero:jazz NÃO histórico  

- artistas:("Miles Davis" "John Coltrane")

Coloque várias cadeias de caracteres entre aspas se quiser que ambas cadeias de caracteres sejam avaliadas como uma única entidade, como neste caso, pesquisar duas cidades distintas no campo `artists`.  

O campo especificado em `fieldName:searchExpression` deve ser um campo `searchable`.  Confira [Criar Índice](/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados em definições de campo.  

> [!NOTE]
> Ao usar expressões de pesquisa em campo, você não precisa usar o `searchFields` parâmetro porque cada expressão de pesquisa em campo tem um nome de campo especificado explicitamente. No entanto, você ainda poderá usar o `searchFields` parâmetro se quiser executar uma consulta em que algumas partes têm o escopo de um campo específico, e o restante pode se aplicar a vários campos. Por exemplo, a consulta `search=genre:jazz NOT history&searchFields=description` corresponderia `jazz` apenas ao `genre` campo, enquanto ela corresponderia ao `NOT history` `description` campo. O nome do campo fornecido em `fieldName:searchExpression` sempre tem precedência sobre o `searchFields` parâmetro, que é o motivo neste exemplo, não precisamos incluir `genre` no `searchFields` parâmetro.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Pesquisa difusa

Uma pesquisa difusa localiza correspondências em termos que têm uma construção semelhante, expandindo um termo até o máximo de 50 termos que atendem aos critérios de distância de dois ou menos. Para obter mais informações, consulte [pesquisa difusa](search-query-fuzzy.md).

Para fazer uma pesquisa difusa, use o símbolo til "~" no final de uma única palavra com um parâmetro opcional, um número entre 0 e 2 (padrão), que especifica a distância de edição. Por exemplo, "mar~" ou "mar~1" retornaria "mar", "amar" e "maré".

A pesquisa difusa só pode ser aplicada a termos, não a frases, mas você pode acrescentar o til a cada termo individualmente em um nome ou frase de várias partes. Por exemplo, "Unviersty ~ de ~" Wshington ~ "corresponderia em" University de Washington ".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Pesquisa de proximidade

As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, `"hotel airport"~5` encontrará os termos "hotel" e "aeroporto" em cinco palavras uma da outra em um documento.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Aumento de termos

O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Isso é diferente dos perfis de pontuação, já que os perfis de pontuação aumentam determinados campos, em vez de termos específicos.  

O exemplo a seguir ajuda a ilustrar as diferenças. Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como *gênero* no [exemplo de índice de loja de música](index-add-scoring-profiles.md#bkmk_ex). O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, `rock^2 electronic` aumentará os documentos que contêm os termos de pesquisa no campo gênero, à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa *rock* serão mais bem classificados do que o outro termo de pesquisa *eletrônico* como resultado do valor de aumento de termo (2).  

 Para aumentar um termo, use o sinal de interpolação, "^", com um fator de aumento (um número) no final do termo que você está pesquisando. Você também pode aumentar as frases. Quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Pesquisa com expressão regular  
 Uma pesquisa de expressão regular localiza uma correspondência com base em padrões que são válidos no Apache Lucene, conforme documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html). No Azure Pesquisa Cognitiva, uma expressão regular é colocada entre barras invertidas `/` .

 Por exemplo, para localizar documentos que contenham "motel" ou "hotel", especifique `/[mh]otel/`. As pesquisas com expressões regulares são comparadas com palavras individuais.

Algumas ferramentas e linguagens impõem requisitos adicionais de caractere de escape. Para o JSON, as cadeias de caracteres que incluem uma barra invertida são repassadas com uma barra invertida: "microsoft.com/azure/" torna-se `search=/.*microsoft.com\/azure\/.*/` o local onde `search=/.* <string-placeholder>.*/` o configura a expressão regular e `microsoft.com\/azure\/` é a cadeia de caracteres com uma barra de escape.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Pesquisa com curinga

Você pode usar a sintaxe geralmente reconhecida para várias `*` pesquisas de curinga de caractere () ou única ( `?` ). Por exemplo, uma expressão de consulta de `search=alpha*` retorna "alfanumérico" ou "alfabética". Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

A sintaxe Lucene completa dá suporte à correspondência de prefixo, infixo e sufixo. No entanto, se tudo o que você precisa é de correspondência de prefixo, você pode usar a sintaxe simples (a correspondência de prefixo tem suporte em ambos).

A correspondência de sufixo, onde `*` ou `?` precede a cadeia de caracteres (como em `search=/.*numeric./` ) ou a correspondência de infixos requer a sintaxe Lucene completa, bem como os delimitadores de barra da expressão regular `/` . Não é possível usar um símbolo * ou ? símbolo como o primeiro caractere de um termo ou dentro de um termo, sem o `/` . 

> [!NOTE]  
> Como regra, a correspondência de padrões é lenta, portanto, talvez você queira explorar métodos alternativos, como a geração de tokens de n-grama de borda, que cria tokens para sequências de caracteres em um termo. O índice será maior, mas as consultas poderão ser executadas mais rapidamente, dependendo da construção do padrão e do comprimento das cadeias de caracteres que estão sendo indexadas.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Impacto de um analisador em consultas curinga

Durante a análise de consulta, as consultas que são formuladas como prefixo, sufixo, curinga ou expressões regulares são passadas como estão para a árvore de consulta, ignorando a [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis). As correspondências só serão encontradas se o índice contiver as cadeias de caracteres no formato especificado pela sua consulta. Na maioria dos casos, você precisará de um analisador durante a indexação que preserva a integridade da cadeia de caracteres para que a correspondência de termos e padrões parciais seja realizada com sucesso. Para obter mais informações, consulte [pesquisa de termo parcial nas consultas de pesquisa cognitiva do Azure](search-query-partial-matching.md).

Considere uma situação em que você pode querer que a consulta de pesquisa ' terminat * ' retorne resultados que contenham termos como ' Terminate ', ' terminação ' e ' encerrações '.

Se você fosse usar o analisador en. Lucene (Inglês Lucene), ele aplicaria uma lematização agressiva de cada termo. Por exemplo, ' Terminate ', ' terminação ', ' finalizações ' serão indexados para baixo até o token ' Termi ' no índice. Por outro lado, os termos em consultas que usam Curingas ou pesquisa difusa não são analisados. portanto, não haveria nenhum resultado que corresponda à consulta ' terminat * '.

Por outro lado, os analisadores da Microsoft (neste caso, o en. Microsoft Analyzer) são um pouco mais avançados e usam lematização em vez de derivar. Isso significa que todos os tokens gerados devem ser palavras válidas em inglês. Por exemplo, ' Terminate ', ' Finalize ' e ' rescisão ' permanecerão em todo o índice e seria uma opção preferida para cenários que dependem muito de curingas e pesquisa difusa.

## <a name="scoring-wildcard-and-regex-queries"></a> Classificar consultas de caractere curinga e regex

O Azure Pesquisa Cognitiva usa a pontuação baseada em frequência ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) para consultas de texto. No entanto, para consultas curinga e regex em que o escopo de termos pode ser potencialmente amplo, o fator de frequência é ignorado para impedir que a classificação seja direcionada para correspondências de termos mais raros. Todas as correspondências são tratadas da mesma maneira para as pesquisas de caractere curinga e regex.

## <a name="special-characters"></a>Caracteres especiais

Em algumas circunstâncias, talvez você queira procurar um caractere especial, como um Emoji ' ❤ ' ou o sinal ' € '. Nesses casos, certifique-se de que o analisador usado não filtre esses caracteres. O analisador padrão ignora muitos caracteres especiais, excluindo-os do índice.

Os analisadores que exportam caracteres especiais incluem o analisador "Whitespace", que leva em consideração quaisquer sequências de caracteres separadas por espaços em branco como tokens (portanto, a cadeia de caracteres "❤" seria considerada um token). Além disso, um analisador de linguagem como o Microsoft English Analyzer ("en. Microsoft"), levaria a cadeia de caracteres "€" como um token. Você pode [testar um analisador](/rest/api/searchservice/test-analyzer) para ver quais tokens ele gera para uma determinada consulta.

Ao usar caracteres Unicode, certifique-se de que os símbolos tenham um escape correto na URL de consulta (por exemplo, para "❤" usaria a sequência de escape `%E2%9D%A4+` ). O postmaster faz essa tradução automaticamente.  

## <a name="precedence-grouping"></a>Precedência (agrupamento)

Você pode usar parênteses para criar subconsultas, incluindo operadores dentro da instrução entre parênteses. Por exemplo, `motel+(wifi|luxury)` irá procurar documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas tem como escopo o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi|pool))` pesquisa o campo "comodidadesDoHotel" para "academia" e "wifi" ou "academia" e "piscina".  

## <a name="query-size-limits"></a>Limites de tamanho de consulta

Há um limite para o tamanho das consultas que você pode enviar para o Azure Pesquisa Cognitiva. Especificamente, você pode ter, no máximo, 1024 cláusulas (expressões separadas por AND, OR e assim por diante). Há também um limite de aproximadamente 32 KB em relação ao tamanho de qualquer termo individual em uma consulta. Se seu aplicativo gerar consultas de pesquisa por meio de programação, é recomendável criá-lo de forma que ele não gere consultas de tamanho ilimitado.  

## <a name="see-also"></a>Veja também

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para a pesquisa completa do Lucene](search-query-lucene-examples.md)
+ [Pesquisar documentos](/rest/api/searchservice/Search-Documents)
+ [Sintaxe de expressão OData para filtros e classificação](query-odata-filter-orderby-syntax.md)   
+ [Sintaxe de consulta simples no Azure Pesquisa Cognitiva](query-simple-syntax.md)
