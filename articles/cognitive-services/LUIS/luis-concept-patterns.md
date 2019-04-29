---
title: Padrões ajudam na previsão
titleSuffix: Language Understanding - Azure Cognitive Services
description: Um padrão permite que você alcance maior precisão para uma intenção sem fornecer muitos enunciados a mais.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 2a160ab7447304dc6eb14f76a723df4e8a4d9f46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813578"
---
# <a name="patterns-improve-prediction-accuracy"></a>Padrões aumentam a precisão da previsão
Os padrões são definidos para aumentar a precisão quando várias expressões são muito similares.  Um padrão permite que você alcance maior precisão para uma intenção sem fornecer muitos enunciados a mais. 

## <a name="patterns-solve-low-intent-confidence"></a>Os padrões resolvem baixa confiabilidade de intenção
Considere um aplicativo de recursos humanos que relata o organograma em relação a um funcionário. Dado o nome e a relação de um funcionário, LUIS retorna os funcionários envolvidos. Considere um funcionário, Tom, com uma gerente chamada Alice e uma equipe de subordinados chamados: Michael, Rebecca e Carl.

![Imagem do organograma](./media/luis-concept-patterns/org-chart.png)

|Declarações|Intenção prevista|Pontuação da intenção|
|--|--|--|
|Quem está subordinado a Tom?|GetOrgChart|.30|
|Quem é subordinado de Tom?|GetOrgChart|.30|

Se um aplicativo tiver entre 10 e 20 expressões com comprimentos diferentes de frase, ordem de palavras diferentes e diferentes palavras (sinônimos de "subordinadas", "gerente", "relatório"), LUIS pode retornar uma baixa pontuação de confiabilidade. Crie um padrão para ajudar LUIS a entender a importância da ordem de palavra. 

Os padrões resolvem as situações a seguir: 

* A pontuação de intenção é baixa
* A intenção correta não é a pontuação superior, mas muito próximos à pontuação superior. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Os padrões não são uma garantia de intenção
Os padrões usam uma mistura de tecnologias de previsão. Configurar uma intenção para uma expressão de modelo em um padrão não é uma garantia da previsão da intenção, mas é um sinal forte. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Padrões não melhorar a detecção de entidade de aprendizado de máquina

Um padrão destina-se principalmente para ajudar a previsão das intenções e funções. A entidade pattern.any é usada para extrair as entidades de forma livre. Embora padrões usam entidades, um padrão não ajuda a detectar uma entidade de aprendizado de máquina.  

Não espere ver melhorias na previsão de entidade se você recolher várias declarações em um único padrão. Para entidades simples serem acionadas, você precisará adicionar declarações ou usar entidades de lista; caso contrário, seu padrão não será acionado.

## <a name="patterns-use-entity-roles"></a>Os padrões usam funções de entidades
Se duas ou mais entidades em um padrão estiverem relacionadas contextualmente, os padrões usarão as [funções](luis-concept-roles.md) da entidade para extrair informações contextuais sobre as entidades.  

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuação da previsão com e sem padrões
Dadas expressões de exemplo suficientes, LUIS poderá aumentar a confiabilidade da previsão sem os padrões. Os padrões de aumentam a pontuação de confiabilidade sem ter que fornecer tantas expressões.  

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é correspondido com base na detecção das entidades dentro do padrão primeiro, em seguida, validando o restante das palavras e a ordem de palavras do padrão. As entidades são exigidas no padrão para que um padrão corresponda. O padrão é aplicado no nível de token, não no nível do caractere. 

## <a name="pattern-syntax"></a>Sintaxe de padrões
Sintaxe de padrão é um modelo para uma expressão. O modelo deverá conter palavras e entidades que você deseja corresponder, bem como palavras e pontuação que você deseja ignorar. **Não** é uma expressão regular. 

As entidades em padrões estão entre chaves, `{}`. Os padrões podem incluir entidades e entidades com funções. [Pattern.Any](luis-concept-entity-types.md#patternany-entity) é uma entidade usada apenas em padrões. 

Sintaxe padrão oferece suporte a seguinte sintaxe:

|Função|Sintaxe|Nível de aninhamento|Exemplo|
|--|--|--|--|
|entidade| {} -colchetes|2|Onde está o formato {nome da entidade}?|
|opcional|[] - colchetes<BR><BR>Há um limite de 3 em níveis de aninhamento de qualquer combinação de opcionais e agrupamento |2|O ponto de interrogação é opcional [?]|
|agrupamento|() - parênteses|2|is (a \| b)|
|ou o| \| -vertical bar (barra vertical)<br><br>Há um limite de 2 nas barras verticais (ou) em um grupo |-|Onde está o formulário ({formulário nome-short} &#x7c; {longa de nome do formulário} &#x7c; {número do formulário})| 
|início e/ou o final da expressão|^-cursor|-|^ começam a expressão<br>a expressão é feito ^<br>^ estrita correspondência literal da expressão de inteiro com a entidade {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Sintaxe de aninhamento em padrões

O **opcional** sintaxe entre colchetes, pode ser aninhadas dois níveis. Por exemplo: `[[this]is] a new form`. Este exemplo permite que as declarações a seguir: 

|Exemplo de expressão opcionais aninhados|Explicação|
|--|--|
|Isso é um novo formulário|corresponde a todas as palavras no padrão|
|é uma nova forma|corresponde à palavra opcional externa e palavras não opcionais no padrão|
|um novo formulário|somente palavras de correspondências necessárias|

O **agrupamento** sintaxe, com parênteses, pode ser aninhadas dois níveis. Por exemplo: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Esse recurso permite que qualquer uma das três entidades a serem correspondidos. 

Se Entity1 é um local com funções como (Seattle) de origem e destino (Cairo) e 2 da entidade é um nome conhecido de construção de uma entidade de lista (RedWest-C), as declarações a seguir seriam mapeados para esse padrão:

|Exemplo de expressão de agrupamento aninhado|Explicação|
|--|--|
|RedWest-C|corresponde à entidade do agrupamento externo|
|Seattle|corresponde a uma das entidades agrupamento interno|
|Cairo|corresponde a uma das entidades agrupamento interno|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites de aninhamento de grupos com sintaxe opcional

Uma combinação de **agrupando** com **opcional** sintaxe tem um limite de 3 níveis de aninhamento.

|Permitido|Exemplo|
|--|--|
|Sim|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Não |( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de aninhamento de grupos com sintaxe ing ou

Uma combinação de **agrupando** com **ing ou** sintaxe tem um limite de 2 barras verticais.

|Permitido|Exemplo|
|--|--|
|Sim|(test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|Não |(test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

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

As palavras do título do livro não são confusas LUIS porque LUIS sabe onde o título de livro termina, com base na entidade Pattern.any.

## <a name="explicit-lists"></a>Listas explícitas

criar uma [lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) por meio da API de criação para permitir a exceção quando:

* O padrão contém um [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* E que a sintaxe padrão permite que a possibilidade de uma extração de entidade incorreto com base na declaração. 

Por exemplo, supondo que você tem um padrão que com sintaxe opcional, `[]`, e sintaxe de entidade, `{}`, combinadas de uma forma para extrair dados incorretamente.

Considere o padrão '[localizar] email sobre {assunto} [de {pessoa}]'.

Nas expressões a seguir, as entidades **assunto** e **pessoa** são extraídas correta e incorretamente:

|Enunciado|Entidade|Extração correta|
|--|--|:--:|
|email sobre cachorros de Chris|assunto=cachorros<br>pessoa=Chris|✔|
|email sobre o homem de La Mancha|assunto=o homem<br>pessoa=La Mancha|X|

Na tabela anterior, a entidade deve ser `the man from La Mancha` (um título de livro), mas como o assunto inclui a palavra opcional `from`, o título é previsto incorretamente. 

Para corrigir esta expressão no padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade {assunto} usando a [API de criação para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional em uma expressão de exemplo
Marque texto opcional na expressão usando a sintaxe colchete de expressão regular, `[]`. O texto opcional pode aninhar colchetes até apenas dois colchetes.

|Padrão com texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` e `from {person}` são opcionais|
|' Você pode ajudar a me [?] | Os sinais de pontuação é opcional|

Marcas de pontuação (`?`, `!`, `.`) deve ser ignorado e você precisa para ignorá-los usando a sintaxe de colchete quadrado em padrões. 

## <a name="pattern-only-apps"></a>Aplicativos de padrão
Você pode criar um aplicativo com as intenções que não têm declarações nenhum exemplo, desde que exista um padrão para cada tentativa. Para um aplicativo somente padrão, o padrão não deve conter entidades aprendidas por máquina porque eles requerem declarações de exemplo. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
