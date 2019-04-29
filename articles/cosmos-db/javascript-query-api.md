---
title: Trabalhando com a API de consulta integrada à linguagem do JavaScript no Azure Cosmos DB
description: Este artigo apresenta os conceitos da API de consulta integrada à linguagem do JavaScript para criar procedimentos armazenados e gatilhos no Azure Cosmos DB.
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 12/08/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 101b5382eaa01ed87f05d83c82002fa1b93144b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043219"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>API de consulta JavaScript no Azure Cosmos DB

Além de emitir consultas usando a API do SQL no Azure Cosmos DB, o [SDK do servidor do Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/) permite que você execute consultas otimizadas usando uma interface JavaScript. Você não precisa estar ciente da linguagem SQL para usar essa interface JavaScript. A API de consulta do JavaScript permite que você crie consultas de forma programática passando funções de predicado em uma sequência de chamadas de função, com uma sintaxe conhecida para bibliotecas JavaScript internas e populares da matriz do ECMAScript5, como Lodash. As consultas são analisadas no tempo de execução do JavaScript e executadas com eficiência usando índices do Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>Funções do JavaScript com suporte

| **Função** | **Descrição** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Inicia uma chamada encadeada que deve ser terminada com value().|
|`filter(predicateFunction [, options] [, callback])`|Filtra a entrada usando uma função de predicado que retorna true/false para filtrar documentos de entrada no conjunto resultante. Essa função se comporta semelhante a uma cláusula WHERE no SQL.|
|`flatten([isShallow] [, options] [, callback])`|Combina e nivela as matrizes de cada item de entrada em uma única matriz. Essa função se comporta semelhante a uma cláusula SelectMany no LINQ.|
|`map(transformationFunction [, options] [, callback])`|Aplica uma projeção dada uma função de transformação que mapeia cada item de entrada para um objeto ou valor JavaScript. Essa função se comporta semelhante a uma cláusula SELECT no SQL.|
|`pluck([propertyName] [, options] [, callback])`|Essa função é um atalho para um mapa que extrai o valor de uma única propriedade de cada item de entrada.|
|`sortBy([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando os documentos no fluxo de documentos de entrada em ordem ascendente usando o predicado fornecido. Essa função se comporta semelhante a uma cláusula ORDER BY no SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando os documentos no fluxo de documentos de entrada em ordem decrescente usando o predicado fornecido. Essa função se comporta semelhante a uma cláusula ORDER BY x DESC no SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Executa uma autojunção com a matriz interna e adiciona os resultados de ambos os lados como tuplas à projeção de resultados. Por exemplo, o ingresso de um documento de pessoas com person.pets produzirá as tuplas [pessoas, animais de estimação]. Isso é semelhante a SelectMany no LINK do .NET.|

Quando incluídas em funções de predicado e/ou do seletor, os constructos do JavaScript a seguir são automaticamente otimizados para serem executados diretamente nos índices do Azure Cosmos DB:

- Operadores simples: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literais, incluindo o literal de objeto: {}
- var, return

Os seguintes constructos do JavaScript não são otimizados pelos índices do Azure Cosmos DB:

- Fluxo de controle (por exemplo, if, for, while)
- Chamadas de função

Para obter mais informações, confira a [Documentação do JavaScript do servidor do Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Folha de referências do SQL para JavaScript

A tabela a seguir apresenta várias consultas SQL e as consultas JavaScript correspondentes. Assim como acontece com consultas SQL, as propriedades (por exemplo, item.id) diferenciam maiúsculas de minúsculas.

> [!NOTE]
> `__` (sublinhado duplo) é um alias para `getContext().getCollection()` ao usar a API de consulta do JavaScript.

|**SQL**|**API de Consulta do JavaScript**|**Descrição**|
|---|---|---|
|SELECIONAR *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|Resulta em todos os documentos (paginados com token de continuação) no estado em que se encontram.|
|SELECIONAR <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projeta a ID, a mensagem (com o alias msg) e a ação de todos os documentos.|
|SELECIONAR *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|Consulta documentos com o predicado : id = "X998_Y998".|
|SELECIONAR *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|Consulta documentos com uma propriedade Tags e Tags é uma matriz que contém o valor 123.|
|SELECIONAR<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Consulta documentos com um predicado, id = "X998_Y998" e projeta a ID e a mensagem (com alias para msg).|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtra documentos que têm uma propriedade de matriz, Tags, e classifica os documentos resultantes pela propriedade do sistema do carimbo de data/hora _ts e projeta + mescla a matriz Tags.|

## <a name="next-steps"></a>Próximas etapas

Conheça mais conceitos e saiba como escrever e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB:

- [Como escrever procedimentos armazenados e gatilhos usando a API de Consulta do JavaScript](how-to-write-javascript-query-api.md)
- [Trabalhando com procedimentos armazenados, gatilhos e funções definidas pelo usuário do Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Como usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Referência de API do lado do servidor JavaScript do Azure Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)

<!-- Update_Description: update meta properties -->