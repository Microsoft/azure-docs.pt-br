---
title: Tipos de entidade
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Entidades de extrair dados de declaração. Tipos de entidade oferecem previsível extração de dados. Há dois tipos de entidades: computador aprendeu e não computador aprendeu. É importante saber que tipo de entidade que você está trabalhando com em declarações.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 59a05e7a20f6b229b37977a75d22611c0d5c31d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813191"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Tipos de entidade e suas finalidades no LUIS

Entidades de extrair dados de declaração. Tipos de entidade oferecem previsível extração de dados. Há dois tipos de entidades: computador aprendeu e não computador aprendeu. É importante saber que tipo de entidade que você está trabalhando com em declarações. 

## <a name="entity-compared-to-intent"></a>Entidade comparada com a intenção

A entidade representa uma palavra ou frase dentro da declaração que você deseja que seja extraída. Uma declaração pode incluir muitas entidades ou nenhuma. Um aplicativo cliente, talvez seja necessário a entidade para realizar sua tarefa ou usá-lo como um guia de várias opções para apresentar ao usuário. 

Uma entidade:

* Representa uma classe, incluindo uma coleção de objetos semelhantes (lugares, coisas, pessoas, eventos ou conceitos). 
* Descreve as informações relevantes para a intenção


Por exemplo, um aplicativo de Pesquisa de Notícias pode incluir entidades como "tópico", "origem", "palavra-chave" e "data de publicação", que são dados importantes para pesquisar notícias. Em um aplicativo de reservas de viagem, o "local", "data", "companhia aérea", "classe da viagem" e "passagens" são informações importantes para reserva de voos (relevantes para a intenção "Reservas de Voo").

Por comparação, a intenção representa a previsão de toda a declaração. 

## <a name="entities-help-with-data-extraction-only"></a>Entidades ajudam apenas com a extração de dados

Rotular ou marcar entidades apenas para fins de extração de entidades, não ajuda na previsão de intenção.

## <a name="entities-represent-data"></a>Entidades representam dados

Entidades são dados que você deseja extrair da declaração. Elas podem ser um nome, data, nome de produto ou qualquer grupo de palavras. 

|Enunciado|Entidade|Dados|
|--|--|--|
|Comprar três passagens para Nova York|Número predefinido<br>Location.Destination|3<br>Nova Iorque|
|Comprar uma passagem de Nova York para Londres em 5 de março|Location.Origin<br>Location.Destination<br>datetimeV2 predefinido|Nova Iorque<br>Londres<br>5 de março de 2018|

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

|Aprendizado de máquina|Pode marcar|Tutorial|Exemplo<br>Response|Tipo de entidade|Finalidade|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Composição**](#composite-entity)|Agrupamento de entidades, independentemente do tipo de entidade.|
|✔|✔|-|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**Hierárquica**](#hierarchical-entity)|Agrupamento de entidades simples.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Lista**](#list-entity)|Lista de itens e seus sinônimos extraídos com correspondência exata do texto.|
|Mista||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entidade na qual é difícil determinar o final da entidade.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Predefinida**](#prebuilt-entity)|Já treinada para extrair vários tipos de dados.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Expressão regular**](#regular-expression-entity)|Usa expressão regular para corresponder ao texto.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Simples**](#simple-entity)|Contém um único conceito em palavra ou frase.|

Somente as entidades computador aprendeu precisam ser marcadas em declarações de exemplo. Entidades de aprendizado de máquina funcionam melhor quando testadas por meio de [consultas de ponto de extremidade](luis-concept-test.md#endpoint-testing) e [revisando os enunciados de ponto de extremidade](luis-how-to-review-endoint-utt.md). 

As entidades Pattern.any devem ser marcadas nos exemplos de modelo [Padrão](luis-how-to-model-intent-pattern.md), não exemplos de usuários de intenção. 

Entidades mistas usam uma combinação de métodos de detecção de entidade.

## <a name="composite-entity"></a>Entidade composta

Uma entidade composta é constituída de outras entidades como entidades predefinidas, expressão regular, simples, lista e entidades hierárquicas. As entidades separadas formam uma entidade inteira. 

Essa entidade é uma boa opção quando os dados:

* Estão relacionados uns aos outros. 
* Estão relacionadas entre si no contexto do enunciado.
* Usam uma variedade de tipos de entidade.
* Precisam ser agrupados e processados pelo aplicativo cliente como uma unidade de informação.
* Tenham uma variedade de enunciados de usuário que exigem aprendizado de máquina.

![entidade composta](./media/luis-concept-entities/composite-entity.png)

[Tutorial](luis-tutorial-composite-entity.md)<br>
[Exemplo de resposta JSON para entidade](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>Entidade hierárquica

**Entidades hierárquicas eventualmente serão preteridas. Use [funções de entidade](luis-concept-roles.md) para determinar os subtipos de entidade, em vez de entidades hierárquicas.**

Uma entidade hierárquica é uma categoria de entidades simples aprendidas contextualmente chamadas de filhos.

![entidade hierárquica](./media/luis-concept-entities/hierarchical-entity.png)

### <a name="roles-versus-hierarchical-entities"></a>Papéis versus entidades hierárquicas

[Funções](luis-concept-roles.md) resolver o mesmo problema como entidades hierárquicas, mas se aplicam a todos os tipos de entidade.  

## <a name="list-entity"></a>Entidade de lista

As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas, juntamente com seus sinônimos. O LUIS não descobre valores adicionais para entidades de lista. Use o recurso **Recomendado** para consultar sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade será retornada na consulta de ponto de extremidade. 

A entidade é uma boa opção quando os dados de texto:

* São um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para esse tipo de entidade.
* O texto no enunciado corresponde exatamente a um sinônimo ou ao nome canônico. LUIS não usa a lista além das correspondências de texto exatas. Lematização, plurais e outras variações não são resolvidas com uma entidade de lista. Para gerenciar variações, considere usar um [padrão](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

![entidade de lista](./media/luis-concept-entities/list-entity.png)

[Tutorial](luis-quickstart-intent-and-list-entity.md)<br>
[Exemplo de resposta JSON para entidade](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Entidade pattern.any

Pattern.any é um espaço reservado de tamanho variável usado apenas em um enunciado de modelo para marcar onde a entidade começa e termina.  

A entidade é uma boa opção quando:

* O final da entidade pode ser confundido com o texto restante do enunciado. 
[Tutorial](luis-tutorial-pattern.md)<br>
[Exemplo de resposta JSON para entidade](luis-concept-data-extraction.md#patternany-entity-data)

**Exemplo**  
Em um determinado aplicativo cliente que pesquisa livros com base no título, o pattern.any extrai o título completo. Um enunciado de modelo usando pattern.any para essa busca de livro é `Was {BookTitle} written by an American this year[?]`. 

Na tabela a seguir, cada linha tem duas versões do enunciado. O enunciado superior é como o LUIS inicialmente verá o enunciado, em que há pouca clareza onde começa e termina o título do livro. O enunciado inferior é como LUIS reconhecerá o título do livro quando um padrão estiver preparado para extração. 

|Enunciado|
|--|
|Foi Man Who Mistook His Wife for a Hat and Other Clinical Tales escrito por um americano este ano?<br>**The Man Who Mistook His Wife for a Hat and Other Clinical Tales** foi escrito por um americano este ano?|
|Foi Half Asleep in Frog Pajamas escrito por um americano este ano?<br>**Half Asleep in Frog Pajamas** foi escrito por uma americano este ano?|
|Foi The Particular Sadness of Lemon Cake: Um romance escrito por um americano este ano?<br>**The Particular Sadness of Lemon Cake: foi um romance escrito** por um americano este ano?|
|Foi There's A Wocket In My Pocket! escrito por um americano este ano?<br>**There's A Wocket In My Pocket!** foi escrito por um americano este ano?|

## <a name="prebuilt-entity"></a>Entidade predefinida

Entidades predefinidas são tipos internos que representam conceitos comuns como email, URL e número de telefone. Os nomes da entidade predefinida são reservados. [Todas as entidades predefinidas](luis-prebuilt-entities.md) adicionadas ao aplicativo são retornadas na consulta de previsão de ponto de extremidade, se forem localizadas na expressão. 

A entidade é uma boa opção quando:

* Os dados correspondem a um caso de uso comum com suporte por entidades predefinidas para a cultura do idioma. 

As entidades predefinidas podem ser adicionadas e removidas a qualquer momento.

![Numerar entidade predefinida](./media/luis-concept-entities/number-entity.png)

[Tutorial](luis-tutorial-prebuilt-intents-entities.md)<br>
[Exemplo de resposta JSON para entidade](luis-concept-data-extraction.md#prebuilt-entity-data)

Algumas dessas entidades predefinidas são definidas no projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) de software livre. Se não houver suporte à sua cultura ou entidade específica, contribua para o projeto. 

### <a name="troubleshooting-prebuilt-entities"></a>As entidades predefinidas de solução de problemas

No portal do LUIS, se uma entidade predefinida é marcada em vez de sua entidade personalizada, você tem algumas opções de como corrigir esse problema.

As entidades predefinidas adicionadas ao aplicativo serão _sempre_ retornado, mesmo se a expressão deve extrair entidades personalizadas para o mesmo texto. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Alterar entidade marcada na expressão de exemplo

Se a entidade predefinida é o mesmo texto ou tokens como a entidade personalizada, selecione o texto na declaração de exemplo e altere a expressão marcada. 

Se a entidade predefinida é marcada com mais texto ou tokens que sua entidade personalizada, você tem algumas opções de como corrigir isso:

* [Remova a expressão de exemplo](#remove-example-utterance-to-fix-tagging) método
* [Remover entidade predefinida](#remove-prebuilt-entity-to-fix-tagging) método

#### <a name="remove-example-utterance-to-fix-tagging"></a>Remova a expressão de exemplo para corrigir a marcação 

Sua primeira opção é remover a expressão de exemplo. 

1. Exclua a expressão de exemplo.
1. Treinar novamente o aplicativo. 
1. Adicione novamente apenas a palavra ou frase que são a entidade, que é marcada como uma entidade predefinida, como uma expressão de exemplo completo. A palavra ou frase ainda terão a entidade predefinida marcada. 
1. Selecione a entidade na declaração de exemplo sobre o **intenção** página e alterar a sua entidade personalizada e treinar novamente. Isso deve impedir que LUIS marcando esse texto exato como a entidade predefinida em quaisquer declarações de exemplo que usam esse texto. 
1. Adicione a expressão inteira de exemplo original novamente à intenção. A entidade personalizada deve continuar a ser marcado como em vez de entidade predefinida. Se a entidade personalizada não for marcada, você precisa adicionar mais exemplos do que o texto em declarações.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Remover entidade predefinida para corrigir a marcação

1. Remova a entidade predefinida do aplicativo. 
1. Sobre o **intenção** página, marque a entidade personalizada na declaração de exemplo.
1. Treine o aplicativo.
1. Adicionar a entidade predefinida para o aplicativo e treinar o aplicativo. Essa correção pressupõe que a entidade predefinida não faz parte de uma entidade de composição.

## <a name="regular-expression-entity"></a>Entidade de expressão regular 

Uma expressão regular é melhor para texto de enunciado bruto. Não diferencia maiúsculas de minúsculas e ignora a variante cultural.  A correspondência de expressão regular é aplicada após alterações ortográficas no nível do caractere, não no nível do token. Se a expressão regular for muito complexa, como o uso de vários colchetes, não será possível adicionar a expressão ao modelo. Usa a parte, mas nem todos os [Regex .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) biblioteca. 

A entidade é uma boa opção quando:

* Os dados são consistentemente formatados com qualquer variação que também seja consistente.
* A expressão regular não requer mais de 2 níveis de aninhamento. 

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

[Tutorial](luis-quickstart-intents-regex-entity.md)<br>
[Exemplo de resposta JSON para entidade](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>Entidade simples 

Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida pelo contexto de aprendizado de máquina. Como entidades simples geralmente são nomes como nomes de empresas, nomes de produtos ou outras categorias de nomes, adicione uma [lista de frases](luis-concept-feature.md) ao usar uma entidade simples para aumentar o sinal dos nomes usados. 

A entidade é uma boa opção quando:

* Os dados não são formatados consistentemente, mas indicam a mesma coisa. 

![entidade simples](./media/luis-concept-entities/simple-entity.png)

[Tutorial](luis-quickstart-primary-and-secondary-data.md)<br/>
[Exemplo de resposta para entidade](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>Limites de entidade

Examine os [limites](luis-boundaries.md#model-boundaries) para entender a quantidade de cada tipo de entidade que você pode adicionar a um modelo.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se você precisar de mais do que o número máximo de entidades 

Você talvez precise usar entidades de composição em combinação com as funções de entidade.

As entidades compostas representam partes de um todo. Por exemplo, uma entidade composta denominada PlaneTicketOrder pode ter as entidades filho Airline, Destination, DepartureCity, DepartureDate e PlaneTicketClass.

O LUIS também fornece o tipo de entidade de lista que não é aprendizado de máquina, mas permite que o aplicativo LUIS especifique uma lista fixa de valores. Confira a referência [Limites do LUIS](luis-boundaries.md) para examinar os limites do tipo de entidade de lista. 

Se você considerou essas entidades e ainda precisa de mais do que o limite, entre em contato com o suporte. Para fazer isso, colete informações detalhadas sobre o sistema, acesse o site do [LUIS](luis-reference-regions.md#luis-website) e, em seguida, selecione **Suporte**. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos sobre [declarações](luis-concept-utterance.md) boas. 

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.
