---
title: Sintaxe de expressão de consulta – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba como usar a sintaxe de expressão de consulta na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 95c2e9d3f54f967b3ebb434c742f69251b80573e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61336749"
---
# <a name="query-expression-syntax"></a>Sintaxe de expressão de consulta

Já vimos que a resposta a uma solicitação **interpret** inclui uma expressão de consulta. A gramática que interpretou a consulta do usuário criou uma expressão de consulta para cada interpretação. Uma expressão de consulta pode ser usada para emitir uma solicitação **evaluate** para recuperar resultados da pesquisa de entidade.

Você também pode criar suas próprias expressões de consulta e usá-las em uma solicitação **evaluate**. Isso pode ser útil se você estiver criando sua própria interface do usuário que cria uma expressão de consulta em resposta a ações do usuário. Para fazer isso, você precisa saber a sintaxe para expressões de consulta.  

Cada atributo de entidade que pode ser incluído em uma expressão de consulta tem um tipo de dados específicos e um conjunto de operadores de consulta possíveis. O conjunto de atributos de entidade e os operadores com suporte para cada atributo é especificado em [atributos da entidade](EntityAttributes.md). Uma consulta de valor único requer o atributo para dar suporte à operação *Equals*. Uma consulta de prefixo requer o atributo para dar suporte à operação *StartsWith*. Consultas de intervalos numéricos requerem o atributo para dar suporte à operação *IsBetween*.

Alguns dos dados de entidade são armazenados como atributos compostos, conforme indicado por um ponto '.' no nome do atributo. Por exemplo, informações do autor/afiliação são representadas como um atributo de composição. Contém 4 componentes: AuN, AuId, AfN, AfId. Esses componentes são peça separadas de dados que formam um valor de atributo de entidade único.


**Atributo de cadeia de caracteres: Valor único** (inclui correspondências contra sinônimos)  
IT = 'indexação pela análise semântica latente'  
Composição (AA.AuN = 'sue dumais')

**Atributo de cadeia de caracteres: Valor único exato** (corresponde apenas aos valores canônicos)  
IT = 'indexação pela análise semântica latente'  
Composição (AA.AuN = 'susan t dumais')
     
**Atributo de cadeia de caracteres: Valor do prefixo**   
IT = 'indexação pela semântica latente'...  
Composição(AA.AuN='sue du'...)

**Atributo numérico: Valor único**  
Y=2010
 
**Atributo numérico: Valor do intervalo**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y=\[2010, 2012\) (inclui apenas o valor de limite esquerdo: 2010, 2011)  
Y=\[2010, 2012\] (inclui os dois valores de limite: 2010, 2011, 2012)
 
**Atributo numérico: Valor do prefixo**  
Y = '19'... (qualquer valor numérico que começa com 19) 
 
**Atributo de data: Valor único**  
D='2010-02-04'

**Atributo de data: Valor do intervalo**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**Consultas E/Ou:**  
E (Y = 1985, IT = 'sistemas eletrônicos desordenados')  
Ou (IT = 'eletrônicos sistemas desordenados', IT = 'prática e princípios de tolerância a falhas')  
E(Ou(Y=1985,Y=2008), Ti='sistemas eletrônicos desordenados')
 
**Consultas de composição:**  
Para componentes de consulta de um atributo de composição, você precisa colocar a parte da expressão de consulta que se refere ao atributo de composição na função Composite(). 

Por exemplo, para consultar documentos pelo nome do autor, use a seguinte consulta:
```
Composite(AA.AuN='mike smith')
```
<br>Para consultar documentos por um determinado autor se o autor for uma instituição específica, use a seguinte consulta:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>A função Composite() une as duas partes do atributo de composição. Isso significa que somente obtemos documentos em que um dos autores é "Mike Smith" enquanto estava no Harvard. 

Para consultar documentos por um determinado autor em afiliação com (outros) autores de uma instituição específica, use a seguinte consulta:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Nesta versão, como Composite() é aplicada ao autor e afiliação individualmente antes de And(), podemos obter todos os documentos em que um dos autores é "Mike Smith" e uma das afiliações dos autores é "Harvard". Isso parece semelhante ao exemplo de consulta anterior, mas não é a mesma coisa.

Em geral, considere o exemplo a seguir: Nós temos um atributo composto C que possui dois componentes A e B. Uma entidade pode ter vários valores para C. Estas são nossas entidades:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>A consulta 
```
Composite(And(C.A=1, C.B=2))
```

<br>faz a correspondência apenas das entidades que têm um valor para C em que o componente C.A é 1 e o componente C.B é 2. Somente E1 corresponde a essa consulta.

A consulta 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>faz a correspondência de entidades que têm um valor para C onde C.A é 1 e também tem um valor para C onde C.B é 2. E1 e E2 correspondem a esta consulta.

Observação:  
- Você não pode fazer referência a uma parte de um atributo de composição fora de uma função Composite().
- Você não pode fazer referência a uma parte de dois atributos de composição diferentes dentro da mesma função Composite().
- Você não pode fazer referência a um atributo de composição dentro de uma função Composite().
