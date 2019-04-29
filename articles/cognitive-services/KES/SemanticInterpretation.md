---
title: Interpretação semântica - API do Serviço de Exploração de Conhecimento
titlesuffix: Azure Cognitive Services
description: Aprenda a usar a interpretação semântica na API do Serviço de Exploração de Conhecimento (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814480"
---
# <a name="semantic-interpretation"></a>Interpretação de semântica

A interpretação de semântica associa cada saída semântica com cada caminho interpretado por meio da gramática.  Em particular, o serviço avalia a sequência de instruções nos elementos `tag` transpostos pela interpretação para calcular a saída final.  

Uma instrução pode ser uma atribuição de um literal ou uma variável a outra variável.  Também pode atribuir a saída de uma função com 0 ou mais parâmetros para uma variável.  Cada parâmetro de função pode ser especificado usando um literal ou uma variável.  Se a função não retorna nenhuma saída, a atribuição é omitida.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Uma variável é especificada usando um identificador de nome que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e sublinhado (\_).  Implicitamente, seu tipo é inferido do valor de saída de função ou literal atribuído a ele. 

Abaixo está uma lista de tipos de dados com suporte no momento:

|Type|DESCRIÇÃO|Exemplos|
|----|----|----|
|Cadeia de caracteres|Sequência de 0 ou mais caracteres|"Olá, Mundo!"<br/>""|
|Bool|Valor booliano|verdadeiro<br/>falso|
|Int32|Inteiro com sinal de 32 bits.  -2.1e9 para 2.1e9|123<br/>-321|
|Int64|Inteiro com sinal de 64 bits. -9.2e18 e 9.2e18|9876543210|
|Double|Ponto flutuante de precisão dupla. 1.7e+/-308 (15 dígitos)|123.456789<br/>1.23456789e2|
|Guid|Identificador Global Exclusivo|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Consultar|Expressão de consulta que especifica um subconjunto dos objetos de dados no índice|Todos()<br/>E(*q1*, *q2*)|

## <a name="semantic-functions"></a>Funções semânticas

Há um conjunto interno de funções semânticas.  Eles permitem a construção de consultas sofisticadas e fornecem controle sensível ao contexto sobre interpretações de gramática.

### <a name="and-function"></a>Função And

`query = And(query1, query2);`

Retorna uma consulta composta da interseção de duas consultas de entrada.

### <a name="or-function"></a>Função Or

`query = Or(query1, query2);`

Retorna uma consulta composta da união de duas consultas de entrada.

### <a name="all-function"></a>Função All

`query = All();`

Retorna uma consulta que inclui todos os objetos de dados.

No exemplo a seguir, usamos a função All() para criar interativamente uma consulta com base na interseção de 1 ou mais palavras-chave.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Função None

`query = None();`

Retorna uma consulta que não inclui nenhum objeto de dados.

No exemplo a seguir, usamos a função None() para criar interativamente uma consulta com base na união de 1 ou mais palavras-chave.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Função de Consulta

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Retorna uma consulta que inclui apenas os objetos de dados cujo atributo de *attrName* coincide com o valor *valor* de acordo com a operação especificada *op*, que assume como padrão "eq".  Normalmente, usa um elemento `attrref` para criar uma consulta com base na cadeia de consulta de entrada correspondente.  Se um valor for fornecido ou obtido por outros meios, a função de Query () pode ser usada para criar uma consulta correspondente a esse valor.

O exemplo a seguir, usamos a função Query () para implementar o suporte para especificar publicações acadêmicas de uma década específica.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Função composta

`query = Composite(innerQuery);`

Retorna uma consulta que encapsula uma *innerQuery* composta de correspondências de sub-atributos de um atributo comum de composição *attr*.  O encapsulamento requer o atributo composto *attr* de qualquer objeto de dados correspondente para ter pelo menos um valor que satisfaça individualmente a *innerQuery*.  Observe que uma consulta em sub-atributos de um atributo composto deve ser encapsulada usando a função composta() antes que ela possa ser combinada com outras consultas.

Por exemplo, a seguinte consulta retorna publicações acadêmicas por "harry shum" enquanto estava em "microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Por outro lado, a seguinte consulta retorna publicações acadêmicas em que um dos autores é "harry shum" e uma das afiliações é "microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Função GetVariable

`value = GetVariable(name, scope);`

Retorna o valor da variável *nome* definido no *escopo* especificado.  *nome* é um identificador que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e sublinhado (_).  *escopo* pode ser definido como "sistema" ou "solicitação".  Observe que as variáveis definidas em escopos diferentes são diferentes entre si, inclusive aquelas definidas por meio de saída das funções semânticas.

Variáveis de escopo de solicitação são compartilhados entre todas as interpretações dentro da solicitação atual de interpretar.  Eles podem ser usados para controlar a pesquisa de interpretações pela gramática.

Variáveis de sistema são predefinidas pelo serviço e podem ser usadas para recuperar várias estatísticas sobre o estado atual do sistema.  Abaixo está o conjunto de variáveis de sistema com suporte no momento:

|NOME|Type|DESCRIÇÃO|
|----|----|----|
|IsAtEndOfQuery|Bool|True se a interpretação atual correspondeu todo o texto de consulta de entrada|
|IsBeyondEndOfQuery|Bool|True se a interpretação atual sugeriu conclusões além de texto de consulta de entrada|

### <a name="setvariable-function"></a>Função SetVariable

`SetVariable(name, value, scope);`

Atribui *valor* à variável *nome* no *escopo* especificado.  *nome* é um identificador que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e sublinhado (_).  Atualmente, o único valor válido para *escopo* é “solicitação”.  Não há nenhuma variável de sistema configurável.

Variáveis de escopo de solicitação são compartilhados entre todas as interpretações dentro da solicitação atual de interpretar.  Eles podem ser usados para controlar a pesquisa de interpretações pela gramática.

### <a name="assertequals-function"></a>Função AssertEquals

`AssertEquals(value1, value2);`

Se *value1* e *value2* são equivalentes, a função terá êxito e não terá efeitos colaterais.  Caso contrário, a função falhará e rejeitará a interpretação.

### <a name="assertnotequals-function"></a>Função AssertNotEquals

`AssertNotEquals(value1, value2);`

Se *value1* e *value2* não são equivalentes, a função terá êxito e não terá efeitos colaterais.  Caso contrário, a função falhará e rejeitará a interpretação.


