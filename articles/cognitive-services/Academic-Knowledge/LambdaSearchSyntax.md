---
title: Sintaxe de pesquisa Lambda – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a sintaxe de pesquisa Lambda que pode ser utilizada na API de Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61337781"
---
# <a name="lambda-search-syntax"></a>Sintaxe de Pesquisa Lambda

Cada cadeia de caracteres de consulta de pesquisa *lambda* descreve um padrão de gráfico. Uma consulta deve ter pelo menos um nó inicial, especificar de qual nó do gráfico começamos a passagem. Para especificar um nó inicial, chame o método *MAG.StartFrom()* e passe a ID (s) de um ou mais nó(s) ou um objeto de consulta que especifica as restrições de pesquisa. O método *StartFrom()* tem três sobrecargas. Todos eles usam dois argumentos com o segundo sendo opcional. O primeiro argumento pode ser um inteiro longo, uma coleção enumerável de inteiro longo, ou uma cadeia de caracteres que representa um objeto JSON, com a mesma semântica da pesquisa *json*:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

O processo de criar uma consulta de pesquisa lambda é passar de um nó para outro. Para especificar o tipo de edge para percorrer, use *FollowEdge()* e passe os tipos de edge desejado. *FollowEdge()* recebe um número arbitrário de argumentos de cadeia de caracteres:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Se não nos importam os tipos de edge(s) a seguir, basta omitir *FollowEdge()* entre dois nós: a consulta passará por todos os edges possíveis entre esses dois nós.

Podemos especificar as ações de passagem a serem executadas em um nó por meio de *VisitNode()*, ou seja, se deseja parar este nó e retornar o caminho atual como o resultado ou continuar a explorar o gráfico.  O tipo enumerado *Ação* define dois tipos de ações: *Action.Return* e *Action.Continue*. Podemos passar um valor de enumeração diretamente em *VisitNode()*, ou combiná-los com o operador AND bit-a-bit '&'. Quando dois ação são combinadas, isso significa que ambas as ações serão tomadas. Observação: não use o operador bit-a-bit ' |' em ações. Isso fará com que a consulta finalize sem retornar nada. Ignorar *VisitNode()* entre duas chamadas *FollowEdge()* fará com que a consulta explore incondicionalmente o gráfico após chegar a um nó.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Para *VisitNode()*, também podemos passar em uma expressão lambda do tipo *Expression\<Func\<INode, Action\>\>*, que usa um *INode* e retorna uma ação de passagem:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* fornece interface de acesso a dados *somente leitura* e algumas funções auxiliares integradas em um nó. 

### <a name="basic-data-access-interfaces"></a>Interfaces de acesso a dados básicos

##### <a name="long-cellid"></a>CellID longo

A ID de 64 bits do nó. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(cadeia de caracteres fieldName)

Obtém o valor da propriedade especificada. *T* é o tipo desejado que o campo deve ser interpretado como. Conversão de tipo automática será tentada, se o tipo desejado não pode ser convertido implicitamente do tipo do campo. Observação: quando a propriedade for múltiplos valores, *GetField\<string\>* fará com que a lista a seja serializada em uma cadeia de caracteres Json ["val1", "val2",...]. Se a propriedade não existir, ele gerará uma exceção e anulará a exploração de gráfico atual.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

Indica se existe um campo com o nome fornecido no nó atual.

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

Funciona como *GetField\<string\>(fieldName)*. No entanto, não retorna exceções quando o campo não é encontrado, ele retorna uma cadeia de caracteres vazia ("") em vez disso.

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

Indica se existe a propriedade especificada no nó atual. Mesmo que *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

Indica se a propriedade tem o valor especificado. 

##### <a name="int-countstring-fieldname"></a>int count(string fieldName)

Obter a contagem de valores de uma determinada propriedade. Quando a propriedade não existe, retornará 0.

### <a name="built-in-helper-functions"></a>Funções auxiliares integradas

##### <a name="action-returnifbool-condition"></a>Ação return_if(bool condition)

Retorna *Action.Return* se a condição for *true*. Se a condição for *false* e essa expressão não está associada com outras ações por um operador bit-a-bit AND e a exploração do gráfico será anulada.

##### <a name="action-continueifbool-condition"></a>Action continue_if(bool condition)

Retorna *Action.Continue* se a condição for *true*. Se a condição for *false* e essa expressão não está associada com outras ações por um operador bit-a-bit AND e a exploração do gráfico será anulada.

##### <a name="bool-dicedouble-p"></a>bool dice(double p)

Gera um número aleatório que é maior ou igual a 0.0 e menor que 1.0. Essa função retorna *true* somente se o número for menor ou igual a *p*.

Em comparação com a pesquisa *json*, a pesquisa *lambda* é mais expressiva: As expressões lambda C# podem ser usadas diretamente para especificar padrões de consulta. A seguir, são apresentados dois exemplos.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
