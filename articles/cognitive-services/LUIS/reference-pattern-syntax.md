---
title: Referência de sintaxe de padrão-LUIS
description: Crie entidades para extrair dados de chave de declarações de usuário em aplicativos de Reconhecimento vocal (LUIS). Os dados extraídos são usados pelo aplicativo cliente.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3caccd6766226ce68b371856b081b052c1033f71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542182"
---
# <a name="pattern-syntax"></a>Sintaxe de padrões

Sintaxe de padrão é um modelo para uma expressão. O modelo deve conter palavras e entidades que você deseja corresponder, bem como palavras e [pontuações](luis-reference-application-settings.md#punctuation-normalization) que você deseja ignorar. **Não** é uma expressão regular.

> [!CAUTION]
> Os padrões incluem apenas pais de entidade de aprendizado de máquina, não subentidades.

As entidades em padrões estão entre chaves, `{}`. Os padrões podem incluir entidades e entidades com funções. [Padrão. any](luis-concept-entity-types.md#patternany-entity) é uma entidade usada apenas em padrões.

A sintaxe de padrão oferece suporte à seguinte sintaxe:

|Função|Sintaxe|Nível de aninhamento|Exemplo|
|--|--|--|--|
|entidade| {} -chaves|2|Onde é o formato {Entity-Name}?|
|opcional|[]-colchetes<BR><BR>Há um limite de 3 em níveis de aninhamento de qualquer combinação de opcional e agrupamento |2|O ponto de interrogação é opcional [?]|
|agrupamento|()-parênteses|2|é (a \| b)|
|ou| \| -barra vertical (pipe)<br><br>Há um limite de 2 nas barras verticais (ou) em um grupo |-|Onde é formulário ({Form-Name-Short} &#x7c; {formulário-nome-longo} &#x7c; {formulário-número})|
|início e/ou fim de expressão|^-cursor|-|^ iniciar o expressão<br>o expressão é concluído ^<br>^ correspondência literal estrita de expressão inteira com {Number} entidade ^|

## <a name="nesting-syntax-in-patterns"></a>Sintaxe de aninhamento em padrões

A sintaxe **opcional** , com colchetes, pode ser aninhada em dois níveis. Por exemplo: `[[this]is] a new form`. Este exemplo permite o seguinte declarações:

|Exemplo de expressão aninhado opcional|Explicação|
|--|--|
|Este é um novo formulário|corresponde a todas as palavras no padrão|
|é um novo formulário|corresponde à palavra opcional externa e às palavras não opcionais no padrão|
|um novo formulário|corresponde apenas às palavras necessárias|

A sintaxe de **agrupamento** , com parênteses, pode ser aninhada em dois níveis. Por exemplo: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Esse recurso permite que qualquer uma das três entidades seja correspondida.

Se Entity1 for um local com funções como Origin (Seattle) e Destination (Cairo) e a entidade 2 for um nome de compilação conhecido de uma entidade de lista (RedWest-C), o seguinte declarações mapearia para esse padrão:

|Exemplo de expressão de agrupamento aninhado|Explicação|
|--|--|
|RedWest-C|corresponde à entidade de agrupamento externo|
|Seattle|corresponde a uma das entidades de agrupamento internas|
|Cairo|corresponde a uma das entidades de agrupamento internas|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites de aninhamento de grupos com sintaxe opcional

Uma combinação de **agrupamento** com sintaxe **opcional** tem um limite de 3 níveis de aninhamento.

|Permitido|Exemplo|
|--|--|
|Sim|([(Test1 &#x7c; test2)] &#x7c; test3)|
|Não|([([Test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de aninhamento para grupos com sintaxe or-ing

Uma combinação de **agrupamento** com a sintaxe **ou-ing** tem um limite de duas barras verticais.

|Permitido|Exemplo|
|--|--|
|Sim|(Test1 &#x7c; test2 &#x7c; (test3 &#x7c; TEST4))|
|Não|(Test1 &#x7c; test2 &#x7c; test3 &#x7c; (TEST4 &#x7c; TEST5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade em um modelo de padrão
Para adicionar uma entidade no modelo de padrão, coloque o nome de entidade entre chaves, como `Who does {Employee} manage?`.

|Padrão com entidade|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade e função em um modelo de padrão
Uma função de entidade é denotada como `{entity:role}` com o nome de entidade, seguido por dois-pontos e, em seguida, o nome da função. Para adicionar uma entidade com uma função no modelo de padrão, coloque o nome de entidade e o nome da função entre chaves, como `Book a ticket from {Location:Origin} to {Location:Destination}`.

|Padrão com funções de entidade|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintaxe para adicionar um pattern.any em um modelo de padrão
A entidade Pattern.any permite que você adicione uma entidade de comprimento variado para o padrão. O pattern.any pode ser qualquer comprimento desde que o modelo de padrão seja seguido.

Para adicionar uma entidade **Pattern.any** no modelo padrão, coloque a entidade Pattern.any com as chaves, como `How much does {Booktitle} cost and what format is it available in?`.

|Padrão com a entidade Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Títulos de livro no padrão|
|--|
|Quanto custa **roubar este livro** e em que formato está disponível?|
|Quanto custa **perguntar** e em que formato está disponível?|
|Quanto custa **O curioso incidente do cachorro no tempo de noite** e em que formato está disponível?|

As palavras do título do livro não são confusas para LUIS porque o LUIS sabe onde o título do livro termina, com base no padrão. qualquer entidade.

## <a name="explicit-lists"></a>Listas explícitas

Crie uma [lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) por meio da API de criação para permitir a exceção quando:

* Seu padrão contém um [padrão. any](luis-concept-entity-types.md#patternany-entity)
* E essa sintaxe de padrão permite a possibilidade de uma extração de entidade incorreta com base no expressão.

Por exemplo, supondo que você tem um padrão que com sintaxe opcional, `[]`, e sintaxe de entidade, `{}`, combinadas de uma forma para extrair dados incorretamente.

Considere o padrão '[localizar] email sobre {assunto} [de {pessoa}]'.

Nas expressões a seguir, as entidades **assunto** e **pessoa** são extraídas correta e incorretamente:

|Enunciado|Entidade|Extração correta|
|--|--|:--:|
|email sobre cachorros de Chris|assunto=cachorros<br>pessoa=Chris|✔|
|email sobre o homem de La Mancha|assunto=o homem<br>pessoa=La Mancha|X|

Na tabela anterior, o assunto deve ser `the man from La Mancha` (um título de livro), mas como o assunto inclui a palavra opcional `from` , o título é previsto incorretamente.

Para corrigir esta expressão no padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade {assunto} usando a [API de criação para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional em uma expressão de exemplo
Marque texto opcional na expressão usando a sintaxe colchete de expressão regular, `[]`. O texto opcional pode aninhar colchetes até apenas dois colchetes.

|Padrão com texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` e `from {person}` são opcionais|
|' Você pode me ajudar a [?]|A marca de pontuação é opcional|

As marcas de Pontuação ( `?` , `!` , `.` ) devem ser ignoradas e você precisa ignorá-las usando a sintaxe de colchetes em padrões.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar um padrão. qualquer entidade](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Conceitos de padrões](luis-concept-patterns.md)

Entenda como [a](luis-reference-prebuilt-sentiment.md) opinião é retornada na resposta. JSON.