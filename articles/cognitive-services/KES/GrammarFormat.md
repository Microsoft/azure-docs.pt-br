---
title: Formato de gramática - API de Serviço de Exploração de Conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o formato de gramática na API de KES (Serviço de Exploração de Conhecimento).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 844bd9a88c52fd398fc66c71e59da513c0d7d90d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814868"
---
# <a name="grammar-format"></a>Formato de Gramática

A gramática específica em um arquivo XML que especifica o conjunto ponderado de consultas de linguagem natural que o serviço pode interpretar, bem como a forma como essas consultas de linguagem natural são traduzidas em expressões de consulta semântica.  A sintaxe de gramática está baseada em [SRGS](https://www.w3.org/TR/speech-grammar/), um padrão de W3C para gramáticas de reconhecimento de fala, com extensões para dar suporte a funções semânticas e integração de dados do índice.

O exemplo a seguir descreve cada um dos elementos sintáticos que podem ser usados em uma gramática.  Consulte [este exemplo](#example) para uma gramática completa que demonstra o uso desses elementos no contexto.

### <a name="grammar-element"></a>elemento de gramática

O `grammar` elemento de nível superior no XML de especificação da gramática.  O `root` atributo necessário especifica o nome da regra raiz que define o ponto de partida da gramática.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Elemento de importação

O `import` elemento importa uma definição de esquema de um arquivo externo para habilitar as referências do atributo. O elemento deve ser um filho de elemento de nível superior `grammar` e aparecer antes de quaisquer `attrref` elementos. O `schema` atributo necessário especifica o nome de um arquivo de esquema localizado no mesmo diretório que o arquivo XML de gramática. O `name` elemento obrigatório especifica o esquema de alias que os elementos subsequentes `attrref` usam ao fazer referência a atributos definidos no esquema.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>Elemento de regra

O `rule` elemento define uma regra de gramática, uma unidade estrutural que especifica um conjunto de expressões de consulta que o sistema pode interpretar.  O elemento deve ser um filho de elemento de nível superior`grammar`.  O `id` atributo obrigatório especifica o nome da regra, que é referenciado nos elementos `grammar` ou `ruleref`.

Um `rule` elemento define um conjunto de expansões legais.  Os tokens de texto correspondem à consulta de entrada diretamente.  Os elementos `item` especificam as repetições e alteram as probabilidades de interpretação.  Os elementos `one-of` indicam opções alternativas.  Os elementos `ruleref` permitem a construção de expansões mais complexas para as mais simples.  Os `attrref` elementos permitem correspondências contra valores de atributos do índice.  Os elementos `tag` especificam a semântica da interpretação e podem alterar a probabilidade de interpretação.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>Elemento de exemplo

O elemento `example` opcional especifica as frases de exemplo que podem ser aceitas contendo a definição `rule`.  Isso pode ser usado para obter a documentação e/ou teste automatizado.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>Elemento de item

O elemento `item` agrupa uma sequência de construções de gramática.  Pode ser usado para indicar repetições da sequência de expansão ou para especificar alternativas em conjunto com o `one-of` elemento.

Quando um `item` elemento não é um filho de um `one-of` elemento, ele pode especificar a repetição da sequência de anexo atribuindo o `repeat` atributo para um valor de contagem.  Um valor de contagem de "*n*" (onde *n* é um inteiro) indica que a sequência deve ocorrer exatamente *n* vezes.  Um valor de contagem de "*m*-*n*" permite que a sequência entre *m* e *n* vezes apareça, inclusive.  Um valor de contagem de "*m*-" especifica que a sequência deve aparecer pelo menos *m* vezes.  O atributo `repeat-logprob` opcional pode ser usado para alterar a probabilidade de interpretação de cada repetição adicional além do mínimo.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Quando os `item` elementos aparecem como filhos de um `one-of` elemento, definem o conjunto de alternativas de expansão.  Nesse uso, o atributo `logprob` opcional especifica a probabilidade de log relativa entre as diferentes opções.  Dada uma probabilidade *p* entre 0 e 1, a probabilidade de log correspondente pode ser calculada como log(*p*), onde log() é a função de log natural.  Se não for especificado, `logprob` o padrão é 0, o que não altera a probabilidade de interpretação.  Observe que a probabilidade de log é sempre um valor de ponto flutuante negativo ou 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>Elemento one-of

O `one-of` elemento especifica as expansões alternativas de um dos elementos `item` filho.  Somente elementos `item` podem aparecer dentro de um `one-of` elemento.  As probabilidades relativas entre as diferentes opções que podem ser especificadas por meio do `logprob` atributo em cada filho `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>Elemento ruleref

O `ruleref` elemento especifica expansões válidas por meio de referências a outro `rule` elemento.  Com o uso de `ruleref` elementos, expressões mais complexas podem ser criadas de regras mais simples.  O `uri` obrigatório indica o nome do referenciado `rule` usando a sintaxe "#*rulename*".  Para capturar a saída semântica da regra referenciada, use o atributo `name` opcional para especificar o nome de uma variável à qual a saída semântica é atribuída.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>Elemento attrref

O `attrref` elemento faz referência a um atributo de índice, permitindo a correspondência com base em valores de atributo observado no índice.  O `uri` atributo obrigatório especifica o nome do esquema de índice e o nome de atributo usando a sintaxe "*schemaName*#*attrName*".  Deve haver um elemento `import` anterior que importa o esquema denominado *schemaName*.  O nome do atributo é o nome de um atributo definido no esquema correspondente.

Além de correspondência de entrada do usuário, o `attrref` elemento também retorna um objeto de consulta estruturada como saída que seleciona o subconjunto dos objetos no índice correspondente ao valor de entrada.  Use `name` atributo opcional para especificar o nome da variável em que a saída do objeto de consulta deve ser armazenada.  O objeto de consulta pode ser composto com outros objetos de consulta para formar mais expressões complexas.  Consulte [Interpretação semântica](SemanticInterpretation.md) para obter detalhes.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Conclusão de Consulta

Para dar suporte as conclusões de consulta ao interpretar consultas de usuário parciais, cada atributo de referência deve incluir "starts_with" como uma operação na definição de esquema.  Dado um prefixo de consulta do usuário, `attrref` corresponderá a todos os valores no índice que concluir o prefixo e produzirá cada valor completo como uma interpretação separada da gramática.  

Exemplos:
* A correspondência `<attrref uri="academic#Keyword" name="keyword"/>` contra o prefixo de consulta "dat" gera uma interpretação dos documentos sobre "database", uma interpretação dos documentos sobre "mineração de dados", etc.
* A correspondência `<attrref uri="academic#Year" name="year"/>` contra o prefixo de consulta "200" gera uma interpretação dos documentos em “2000”, uma interpretação para os documentos em “2001” etc.

#### <a name="matching-operations"></a>Operações de Correspondência

Além de correspondência exata, selecione tipos de atributo que também suportam prefixo e desigualdade corresponde via `op` opcional.  Se nenhum objeto no índice tiver um valor correspondente, o caminho de gramática estará bloqueado e o serviço não irá gerar qualquer interpretações sobre este caminho de gramática.   O `op` atributo é "eq".

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

A tabela a seguir lista os valores de suporte `op`para cada tipo de atributo.  Seu uso exige que a operação de índice correspondente seja incluída na definição de atributo de esquema.

| Tipo de Atributo | Valor da Op | DESCRIÇÃO | Operação do Índiec
|----|----|----|----|
| Cadeia de caracteres | eq | Correspondência exata da cadeia de caracteres | equals |
| Cadeia de caracteres | starts_with | Correspondência de prefixo de cadeia de caracteres | starts_with |
| Int32, Int64, Double | eq |  Correspondência de igualdade numérica | equals |
| Int32, Int64, Double | lt, le, gt, ge | Correspondência de desigualdade numérica (<, < =, >, > =) | is_between |
| Int32, Int64, Double | starts_with | Correspondência de prefixo do valor na notação decimal | starts_with |

Exemplos:
* `<attrref uri="academic#Year" op="lt" name="year"/>` corresponde a cadeia de caracteres de entrada "2000" e retorna todos os artigos publicados antes do ano 2000, exclusivamente.
* `<attrref uri="academic#Year" op="lt" name="year"/>` não coincide com a cadeia de caracteres de entrada "20" porque não há nenhum documentos no índice publicado antes do ano 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` corresponde a cadeia de caracteres de entrada "dat" e retorna um documento de interpretação única sobre "database", "mineração de dados", etc.  Esse é um caso de uso incomum.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` corresponde a cadeia de caracteres de entrada "20" e retorna um documentos única interpretação publicada em 299 200, 2000-2999, etc.  Esse é um caso de uso incomum.

### <a name="tag-element"></a>Elemento de marcação

O `tag` elemento Especifica como um caminho por meio de gramática deve ser interpretado.  Contém uma sequência de instruções finalizada por ponto e vírgula.  Uma instrução pode ser uma atribuição de um literal ou uma variável a outra variável.  Também pode atribuir a saída de uma função com 0 ou mais parâmetros para uma variável.  Cada parâmetro de função pode ser especificado usando um literal ou uma variável.  Se a função não retorna nenhuma saída, a atribuição é omitida.  O escopo da variável é local para a regra que contém.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Cada `rule` na gramática tem uma variável predefinida denominada "saída", que representa a saída de semântica da regra.  Seu valor é calculado pela avaliação de cada uma das instruções semânticas percorridas pelo caminho por meio da `rule` correspondência ao usuário de entrada de consulta.  O valor atribuído à variável "saída" no final da avaliação é a saída semântica da regra.  A saída semântica para interpretar uma consulta de usuário em relação a gramática é a saída semântica da regra raiz.

Algumas instruções podem alterar a probabilidade de um caminho de interpretação introduzindo um deslocamento de probabilidade de log suplementar.  Algumas instruções podem rejeitar a interpretação caso as condições especificadas não forem correspondidas.

Para obter uma lista de funções semânticas com suporte, consulte [Funções Semânticas](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Probabilidade de Interpretação

A probabilidade de um caminho de interpretação por meio de gramática é a probabilidade de log cumulativa de todas os `<item>` elementos e funções semânticas encontradas ao longo do caminho.  Descreve a probabilidade relativa de correspondência de uma determinada sequência de entrada.

Dada uma probabilidade *p* entre 0 e 1, a probabilidade de log correspondente pode ser calculada como log(*p*), onde log() é a função de log natural.  Usar probabilidades de log permite que o sistema acumule a probabilidade de junção de um caminho de interpretação por meio da adição simples.  Isso também evita estouro negativo de ponto flutuante comum para esses cálculos de probabilidade de junção.  Observe que, por design, a probabilidade de log é sempre um valor negativo de ponto flutuante ou 0, onde os valores maiores indicam alta probabilidade.

## <a name="example"></a>Exemplo

Este é um exemplo XML do domínio publicações acadêmicas que demonstra os vários elementos de uma gramática:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
