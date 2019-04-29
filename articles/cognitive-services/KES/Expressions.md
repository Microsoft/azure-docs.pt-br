---
title: Expressões de consulta estruturada - API do Serviço de Exploração de Conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba como usar expressões de consulta estruturada no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815106"
---
# <a name="structured-query-expression"></a>Expressão de consulta estruturada

Uma expressão de consulta estruturada especifica um conjunto de operações para avaliar em relação ao índice de dados.  Ele consiste em expressões de consulta de atributo e funções de nível superior.  Use o método [*avaliar*](evaluateMethod.md) para calcular os objetos que correspondem à expressão.  Este é um exemplo do domínio de publicações acadêmicas que retorna publicações escritas por Jaime Teevan desde o ano de 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Expressões de consulta estruturada podem ser obtidas nas solicitações [*Interpretar*](interpretMethod.md), onde a saída semântica de cada interpretação é uma expressão de consulta estruturada que retorna os objetos de índice correspondentes a consulta de linguagem de entrada natural.  Como alternativa, eles podem ser criados manualmente usando a sintaxe descrita nesta seção.

## <a name="attribute-query-expression"></a>Expressão de consulta de atributo

Uma expressão de consulta de atributo identifica um conjunto de objetos com base na correspondência em relação a um atributo específico.  Operações de correspondência diferentes são suportadas dependendo do tipo de atributo e operação indexada especificada no [esquema](SchemaFormat.md):

| Type | Operação | Exemplos |
|------|-------------|------------|
| Cadeia de caracteres | equals | Título='análise semântica latente' (canônico + sinônimos) |
| Cadeia de caracteres | equals | Author.Name=='susan t dumais' (canônico apenas)|
| Cadeia de caracteres | starts_with | Título='latente'... |
| Int32/Int64/Double | equals | Ano=2000 |
| Int32/Int64/Double | starts_with | Ano='20'... (qualquer valor decimal começando com "20") |
| Int32/Int64/Double | is_between | Ano&lt;2000 <br/> Ano&lt;=2000 <br/> Ano&gt;2000 <br/> Ano&gt;=2000 <br/> Ano=[2010,2012) *(inclui somente o valor do limite esquerdo: 2010, 2011)* <br/> Ano=[2000,2012] *(inclui ambos os valores de limite: 2010, 2011, 2012)* |
| Data | equals | Data de nascimento='1984-05-14' |
| Data | is_between | Data de nascimento&lt;='2008/03/14' <br/> Data de publicação=['2000-01-01','2009-12-31'] |
| Guid | equals | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Por exemplo, a expressão "Título='latente'..." corresponde a todas as publicações acadêmicas cujo título começa com a cadeia de caracteres "latente".  Para avaliar esta expressão, o atributo Título deve especificar a operação "starts_with" no esquema usado para criar o índice.

Para atributos com sinônimos associados, uma expressão de consulta pode especificar objetos cujo valor canônico corresponde a uma determinada cadeia de caracteres usando o operador "==" ou objetos em que qualquer um dos seus valores canônico/sinônimo corresponda usando o operador "=".  Ambos requerem que o operador "equals" seja especificado na definição do atributo.


## <a name="functions"></a>Funções

Há um conjunto interno de funções que permite a construção de expressões de consulta mais sofisticadas nas consultas de atributo básico.

### <a name="and-function"></a>Função And

`And(expr1, expr2)`

Retorna a interseção de duas expressões de consulta de entrada.

O exemplo a seguir retorna publicações acadêmicas publicadas no ano 2000 sobre recuperação de informações:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Função Or

`Or(expr1, expr2)`

Retorna a união de duas expressões de consulta de entrada.

O exemplo a seguir retorna publicações acadêmicas publicadas no ano 2000 sobre recuperação de informações ou modelagem de usuário:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Função composta

`Composite(expr)`

Retorna uma expressão que encapsula uma expressão interna é composta de consultas de sub-atributos de um atributo comum de composição.  O encapsulamento requer o atributo composto de qualquer objeto de dados correspondente para ter pelo menos um valor que satisfaça individualmente a expressão interna.  Observe que uma expressão de consulta em sub-atributos de um atributo composto deve ser encapsulada usando a função composta() antes que ela possa ser combinada com outras expressões de consulta.

Por exemplo, a seguinte expressão retorna publicações acadêmicas por "harry shum" enquanto estava em "microsoft":

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Por outro lado, a seguinte expressão retorna publicações acadêmicas em que um dos autores é "harry shum" e uma das afiliações é "microsoft":

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
