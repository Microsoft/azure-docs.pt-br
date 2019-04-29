---
title: Funções para entidades
titleSuffix: Azure Cognitive Services
description: As funções são subtipos nomeados e contextuais de uma entidade usada apenas em padrões. Por exemplo, na declaração `buy a ticket from New York to London`, New York e London são cidades, mas cada uma tem um significado diferente na frase. Nova York é a cidade de origem e Londres é a cidade de destino.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813834"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Funções de entidade para os subtipos contextuais

As funções permitem que entidades tiver nomeado subtipos. Uma função pode ser usada com qualquer tipo de entidade predefinida ou personalizada e usada em padrões e enunciados de exemplo. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Exemplo de entidade de computador aprendeu de funções

Na declaração "comprar um tíquete do **Nova York** à **Londres**, Nova York e Londres são cidades, mas cada um tem um significado diferente na frase. Nova York é a cidade de origem e Londres é a cidade de destino. 

```
buy a ticket from New York to London
```

As funções dão um nome para essas diferenças:

|Tipo de entidade|Nome da entidade|Função|Finalidade|
|--|--|--|--|
|Simples|Local padrão|origin|de onde o avião parte|
|Simples|Local padrão|destino|onde o avião pousa|

## <a name="non-machine-learned-entity-example-of-roles"></a>Exemplo de entidade não computador aprendeu de funções

Na declaração "Agendar a reunião de 8 a 9", ambos os números indicam um tempo, mas cada vez que tem um significado diferente na declaração. As funções fornecem o nome para as diferenças. 

```
Schedule the meeting from 8 to 9
```

|Tipo de entidade|Nome da função|Value|
|--|--|--|
|datetimeV2 predefinido|StartTime|8|
|datetimeV2 predefinido|EndTime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>São várias entidades em uma expressão com a mesma coisa que funções? 

Várias entidades podem existir em uma expressão e podem ser extraídas sem o uso de funções. Se o contexto da sentença indica com a versão da entidade tem um valor, uma função deve ser usada. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Não use funções de busca de duplicatas sem significado

Se a expressão inclui uma lista de locais, `I want to travel to Seattle, Cairo, and London.`, esta é uma lista em que cada item não tem um significado adicional. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Use as funções se duplicatas indicam significado

Se a expressão inclui uma lista de locais com significado, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, o significado de origem, parada temporária e destino deve ser capturado com as funções.

### <a name="roles-can-indicate-order"></a>As funções podem indicar a ordem

Se a expressão é alterado para indicar a ordem em que você quisesse extrair, `I want to first start with Seattle, second London, then third Cairo`, você pode extrair de duas maneiras. Você pode marcar os tokens que indicam que a função `first start with`, `second`, `third`. Você também pode usar a entidade predefinida **Ordinal** e o **GeographyV2** predefinidos de entidade em uma entidade de composição para capturar a ideia de ordem e o local. 

## <a name="how-are-roles-used-in-example-utterances"></a>Como as funções são usadas em declarações de exemplo?

Quando uma entidade tem uma função e a entidade está marcada em uma expressão de exemplo, você tem a opção de selecionar apenas a entidade, ou a entidade e a função. 

As seguintes declarações de exemplo usarem entidades e funções:

|Exibição de token|Exibição de entidade|
|--|--|
|Tenho interesse em aprender mais sobre **Seattle**|Estou interessado em aprender mais sobre {Location}|
|Comprar um tíquete de Seattle para Nova York|Comprar um tíquete de {local: origem} para {local: destino}|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>Como as funções estão relacionadas às entidades hierárquicas?

Funções agora estão disponíveis para todas as entidades em declarações de exemplo, bem como o uso anterior de padrões. Como eles estão disponíveis em todos os lugares, eles substituem a necessidade de entidades hierárquicas. Novas entidades devem ser criadas com as funções, em vez de usar entidades hierárquicas. 

Entidades hierárquicas eventualmente serão preteridas.

## <a name="how-are-roles-used-in-patterns"></a>Como as funções são usadas em padrões?
Na declaração modelo do padrão, as funções são usadas dentro da declaração: 

|Padrão com funções de entidade|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintaxe da função em padrões
A entidade e a função estão entre parênteses, `{}`. A entidade e a função são separadas por dois-pontos. 

## <a name="entity-roles-versus-collaborator-roles"></a>Funções de entidade em comparação com as funções de Colaborador

As funções de entidade se aplicam ao modelo de dados do aplicativo LUIS. [Colaborador](luis-concept-collaborator.md) as funções se aplicam aos níveis de acesso de criação. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Próximas etapas

* Use uma [tutorial prático](tutorial-entity-roles.md) usando funções de entidade com entidades de computador não aprendeu
* Saiba como adicionar [funções](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
