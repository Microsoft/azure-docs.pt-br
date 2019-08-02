---
title: Tipos de entidade-LUIS
titleSuffix: Azure Cognitive Services
description: 'As entidades extraem dados do expressão. Os tipos de entidade oferecem extração previsível de dados. Há dois tipos de entidades: aprendidas por máquina e não aprendidas por computador. É importante saber para qual tipo de entidade você está trabalhando no declarações.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 9919b6d07e874bd306bdba9da2cd3357bedc48f0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563998"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Tipos de entidade e suas finalidades no LUIS

As entidades extraem dados do expressão. Os tipos de entidade oferecem extração previsível de dados. Há dois tipos de entidades: aprendidas por máquina e não aprendidas por computador. É importante saber para qual tipo de entidade você está trabalhando no declarações. 

## <a name="entity-compared-to-intent"></a>Entidade comparada com a intenção

A entidade representa uma palavra ou frase dentro da declaração que você deseja que seja extraída. Uma declaração pode incluir muitas entidades ou nenhuma. Um aplicativo cliente pode precisar que a entidade execute sua tarefa ou use-a como um guia de várias opções para apresentar ao usuário. 

Uma entidade:

* Representa uma classe que inclui uma coleção de objetos semelhantes (locais, coisas, pessoas, eventos ou conceitos). 
* Descreve as informações relevantes para a intenção


Por exemplo, um aplicativo de Pesquisa de Notícias pode incluir entidades como "tópico", "origem", "palavra-chave" e "data de publicação", que são dados importantes para pesquisar notícias. Em um aplicativo de reservas de viagem, o "local", "data", "companhia aérea", "classe da viagem" e "passagens" são informações importantes para reserva de voos (relevantes para a intenção "Reservas de Voo").

Por comparação, a intenção representa a previsão de toda a declaração. 

## <a name="entities-help-with-data-extraction-only"></a>Entidades ajudam apenas com a extração de dados

Rotular ou marcar entidades apenas para fins de extração de entidades, não ajuda na previsão de intenção.

## <a name="entities-represent-data"></a>Entidades representam dados

Entidades são dados que você deseja extrair da declaração. Elas podem ser um nome, data, nome de produto ou qualquer grupo de palavras. 

|Enunciado|Entidade|Dados|
|--|--|--|
|Comprar três passagens para Nova York|Número predefinido<br>Location.Destination|3<br>Nova York|
|Comprar uma passagem de Nova York para Londres em 5 de março|Location.Origin<br>Location.Destination<br>datetimeV2 predefinido|Nova York<br>Londres<br>5 de março de 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>As entidades são opcionais, mas altamente recomendadas

Embora as intenções sejam obrigatórias, as entidades são opcionais. Você não precisa criar entidades para cada conceito no aplicativo, mas apenas aquelas necessárias para que o aplicativo cliente execute uma ação. 

Se as declarações não tiverem detalhes, seu bot precisará continuar e não será necessário adicioná-las. À medida que seu aplicativo amadurece, você pode adicioná-las depois. 

Se você não tiver certeza de como usar as informações, adicione algumas entidades predefinidas comuns como [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md) e [número de telefone](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Rótulo para o significado da palavra

Se a escolha ou a disposição das palavras for a mesma, mas não significar a mesma coisa, não a rotule com a entidade. 

Nas declarações a seguir, a palavra `fair` é um homógrafo. Sua ortografia é a mesma, mas tem um significado diferente:

|Enunciado|
|--|
|Que tipos de festivais regionais estão acontecendo na área de Seattle neste verão?|
|A classificação atual para a análise de Seattle é justa?|

Se você quiser que uma entidade de evento localize todos os dados do evento, rotule a palavra `fair` na primeira declaração, mas não na segunda.

## <a name="entities-are-shared-across-intents"></a>Entidades são compartilhadas entre intenções

Entidades são compartilhadas entre intenções. Elas não pertencem a uma única intenção. Intenções e entidades podem ser associadas semanticamente, mas não é uma relação exclusivo.

No enunciado "Reserve-me uma passagem para Paris", "Paris" é uma entidade que refere-se ao local. Ao reconhecer as entidades mencionadas no enunciado do usuário, o LUIS ajuda o aplicativo cliente a escolher as ações específicas a serem executadas para atender à solicitação do usuário.

## <a name="mark-entities-in-none-intent"></a>Marcar entidades na intenção None

Todas as intenções, incluindo a intenção **None**, devem ter entidades marcadas, quando possível. Isso ajuda o LUIS a saber mais sobre onde as entidades estão nas declarações e quais palavras estão em torno das entidades. 

## <a name="entity-status-for-predictions"></a>Status da entidade para previsões

O portal do LUIS informa quando a entidade em um exemplo de enunciado é diferente da entidade marcada ou está muito próxima de outra entidade e, portanto, é imprecisa. Isso é indicado por um sublinhado vermelho no enunciado de exemplo. 

Para obter mais informações, consulte [Previsões de Status da Entidade](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Tipos de entidades

O LUIS oferece muitos tipos de entidades. Escolha a entidade com base em como os dados devem ser extraídos e como devem ser representados após extraídos.

As entidades podem ser extraídas com aprendizado de máquina, permitindo que o LUIS continue aprendendo sobre como a entidade aparece no enunciado. As entidades podem ser extraídas sem aprendizado de máquina, correspondendo a um texto exato ou uma expressão regular. Entidades em padrões podem ser extraídas com uma implementação mista. 

Depois que a entidade é extraída, os dados da entidade podem ser representados como uma única unidade de informação ou combinados com outras entidades para formar uma unidade de informação que o aplicativo cliente pode usar.

|Aprendizado de máquina|Pode marcar|Tutorial|Exemplo<br>Resposta|Tipo de entidade|Finalidade|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Composição**](#composite-entity)|Agrupamento de entidades, independentemente do tipo de entidade.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Lista**](#list-entity)|Lista de itens e seus sinônimos extraídos com correspondência exata do texto.|
|Mista||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entidade na qual é difícil determinar o final da entidade.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Predefinida**](#prebuilt-entity)|Já treinada para extrair vários tipos de dados.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Expressão regular**](#regular-expression-entity)|Usa expressão regular para corresponder ao texto.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Simples**](#simple-entity)|Contém um único conceito em palavra ou frase.|

Somente entidades aprendidas por máquina precisam ser marcadas no exemplo declarações. Entidades de aprendizado de máquina funcionam melhor quando testadas por meio de [consultas de ponto de extremidade](luis-concept-test.md#endpoint-testing) e [revisando os enunciados de ponto de extremidade](luis-how-to-review-endoint-utt.md). 

As entidades Pattern.any devem ser marcadas nos exemplos de modelo [Padrão](luis-how-to-model-intent-pattern.md), não exemplos de usuários de intenção. 

Entidades mistas usam uma combinação de métodos de detecção de entidade.

## <a name="machine-learned-entities-use-context"></a>Contexto de uso de entidades aprendidas por computador

As entidades aprendidas por computador aprendem com o contexto no expressão. Isso torna a variação do posicionamento no exemplo declarações significativa. 

## <a name="non-machine-learned-entities-dont-use-context"></a>Entidades não aprendidas por computador não usam contexto

As seguintes entidades não conhecidas por máquina não levam o contexto de expressão em conta ao corresponder entidades: 

* [Entidades predefinidas](#prebuilt-entity)
* [Entidades Regex](#regular-expression-entity)
* [Entidades de lista](#list-entity) 

Essas entidades não exigem rotulagem ou treinamento do modelo. Depois de adicionar ou configurar a entidade, as entidades são extraídas. A desvantagem é que essas entidades podem ser sobrecombinadas, em que, se o contexto tiver sido levado em conta, a correspondência não teria sido feita. 

Isso acontece com as entidades de lista em novos modelos com frequência. Você cria e testa seu modelo com uma entidade de lista, mas quando publica seu modelo e recebe consultas do ponto de extremidade, você percebe que seu modelo está se sobrecorrespondência devido à falta de contexto. 

Se você quiser corresponder palavras ou frases e assumir o contexto, terá duas opções. A primeira é usar uma entidade simples emparelhada com uma lista de frases. A lista de frases não será usada para correspondência, mas, em vez disso, ajudará a sinalizar palavras relativamente semelhantes (lista intercambiável). Se você precisar ter uma correspondência exata em vez das variações de uma lista de frases, use uma entidade de lista com uma função, descrita abaixo.

### <a name="context-with-non-machine-learned-entities"></a>Contexto com entidades não aprendidas por computador

Se você quiser o contexto do expressão para entidades aprendidas que não sejam de máquina, use [funções](luis-concept-roles.md).

Se você tiver uma entidade não aprendida por máquina, como [entidades](#prebuilt-entity)predefinidas, entidades [Regex](#regular-expression-entity) ou entidades de [lista](#list-entity) , que corresponde além da instância desejada, considere a criação de uma entidade com duas funções. Uma função capturará o que você está procurando e uma função irá capturar o que você não está procurando. Ambas as versões precisarão ser rotuladas no exemplo declarações.  

## <a name="composite-entity"></a>Entidade composta

Uma [entidade composta](reference-entity-composite.md) é composta por outras entidades, como entidades predefinidas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira. 

## <a name="list-entity"></a>Entidade de lista

As [entidades de lista](reference-entity-list.md) representam um conjunto fixo e fechado de palavras relacionadas junto com seus sinônimos. O LUIS não descobre valores adicionais para entidades de lista. Use o recurso **Recomendado** para consultar sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade será retornada na consulta de ponto de extremidade. 

## <a name="patternany-entity"></a>Entidade pattern.any

[Padrão. any](reference-entity-pattern-any.md) é um espaço reservado de comprimento variável usado somente no modelo de um padrão expressão para marcar onde a entidade começa e termina.  

## <a name="prebuilt-entity"></a>Entidade predefinida

Entidades predefinidas são tipos internos que representam conceitos comuns como email, URL e número de telefone. Os nomes da entidade predefinida são reservados. [Todas as entidades predefinidas](luis-prebuilt-entities.md) adicionadas ao aplicativo são retornadas na consulta de previsão de ponto de extremidade, se forem localizadas na expressão. 

A entidade é uma boa opção quando:

* Os dados correspondem a um caso de uso comum com suporte por entidades predefinidas para a cultura do idioma. 

As entidades predefinidas podem ser adicionadas e removidas a qualquer momento.

![Numerar entidade predefinida](./media/luis-concept-entities/number-entity.png)

[Tutorial](luis-tutorial-prebuilt-intents-entities.md)<br>
[Exemplo de resposta JSON para entidade](luis-concept-data-extraction.md#prebuilt-entity-data)

Algumas dessas entidades predefinidas são definidas no projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) de software livre. Se não houver suporte à sua cultura ou entidade específica, contribua para o projeto. 

### <a name="troubleshooting-prebuilt-entities"></a>Solucionando problemas de entidades predefinidas

No portal do LUIS, se uma entidade predefinida estiver marcada em vez de sua entidade personalizada, você terá algumas opções de como corrigir isso.

As entidades predefinidas adicionadas ao aplicativo serão _sempre_ retornadas, mesmo que o expressão deva extrair entidades personalizadas para o mesmo texto. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Alterar a entidade marcada no exemplo expressão

Se a entidade predefinida for o mesmo texto ou tokens que a entidade personalizada, selecione o texto no exemplo expressão e altere o expressão marcado. 

Se a entidade predefinida estiver marcada com mais texto ou tokens do que sua entidade personalizada, você terá algumas opções de como corrigir isso:

* [Remover exemplo](#remove-example-utterance-to-fix-tagging) de método expressão
* [Remover](#remove-prebuilt-entity-to-fix-tagging) método de entidade predefinida

#### <a name="remove-example-utterance-to-fix-tagging"></a>Remover o exemplo expressão para corrigir a marcação 

Sua primeira opção é remover o exemplo expressão. 

1. Exclua o exemplo expressão.
1. Readaptação do aplicativo. 
1. Adicione de volta apenas a palavra ou frase que é a entidade, que é marcada como uma entidade predefinida, como um exemplo completo de expressão. A palavra ou frase ainda terá a entidade predefinida marcada. 
1. Selecione a entidade no exemplo expressão na página **intenção** e altere para sua entidade personalizada e treine novamente. Isso deve impedir LUIS de marcar esse texto exato como a entidade predefinida em qualquer declarações de exemplo que use esse texto. 
1. Adicione o exemplo original inteiro expressão de volta à intenção. A entidade personalizada deve continuar sendo marcada em vez da entidade predefinida. Se a entidade personalizada não estiver marcada, você precisará adicionar mais exemplos desse texto em declarações.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Remover a entidade predefinida para corrigir a marcação

1. Remova a entidade predefinida do aplicativo. 
1. Na página **intenção** , marque a entidade personalizada no exemplo expressão.
1. Treine o aplicativo.
1. Adicione a entidade predefinida de volta ao aplicativo e treine o aplicativo. Essa correção pressupõe que a entidade predefinida não faz parte de uma entidade composta.

## <a name="regular-expression-entity"></a>Entidade de expressão regular 

Uma [entidade de expressão regular](reference-entity-regular-expression.md) extrai uma entidade com base em um padrão de expressão regular fornecido por você.

## <a name="simple-entity"></a>Entidade simples

Uma [entidade simples](reference-entity-simple.md) é um valor de aprendizado de máquina. Ele pode ser uma palavra ou frase.
## <a name="entity-limits"></a>Limites de entidade

Examine os [limites](luis-boundaries.md#model-boundaries) para entender a quantidade de cada tipo de entidade que você pode adicionar a um modelo.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se você precisar de mais do que o número máximo de entidades 

Talvez seja necessário usar entidades compostas em combinação com funções de entidade.

As entidades compostas representam partes de um todo. Por exemplo, uma entidade composta denominada PlaneTicketOrder pode ter as entidades filho Airline, Destination, DepartureCity, DepartureDate e PlaneTicketClass.

O LUIS também fornece o tipo de entidade de lista que não é aprendizado de máquina, mas permite que o aplicativo LUIS especifique uma lista fixa de valores. Confira a referência [Limites do LUIS](luis-boundaries.md) para examinar os limites do tipo de entidade de lista. 

Se você considerou essas entidades e ainda precisa de mais do que o limite, entre em contato com o suporte. Para fazer isso, colete informações detalhadas sobre o sistema, acesse o site do [LUIS](luis-reference-regions.md#luis-website) e, em seguida, selecione **Suporte**. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos sobre [declarações](luis-concept-utterance.md) boas. 

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.
