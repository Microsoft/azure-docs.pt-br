---
title: Recursos de aprendizado de máquina com LUIS
description: Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 08ab71375171d4bb4167c725bc7118bec2e1ebfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371976"
---
# <a name="machine-learning-features"></a>Recursos de aprendizado de máquina

No Machine Learning, um *recurso*   é uma característica ou atributo diferenciado dos dados que seu sistema observa e aprende.

Os recursos de aprendizado de máquina fornecem indicações LUIS importantes para onde procurar coisas que distinguem um conceito. Eles são dicas que o LUIS pode usar, mas não são regras rígidas. O LUIS usa essas dicas em conjunto com os rótulos para localizar os dados.

Um recurso pode ser descrito como uma função, como f (x) = y. No exemplo expressão, o recurso informa onde procurar a característica diferencial. Use essas informações para ajudar a criar seu esquema.

## <a name="types-of-features"></a>Tipos de recursos

Os recursos são uma parte necessária do seu design de esquema. O LUIS dá suporte a listas de frases e modelos como recursos:

* Recurso de lista de frases
* Modelo (intenção ou entidade) como um recurso

## <a name="find-features-in-your-example-utterances"></a>Encontre recursos no seu exemplo declarações

Como o LUIS é um aplicativo baseado em linguagem, os recursos são baseados em texto. Escolha o texto que indica a característica que você deseja distinguir. Para LUIS, a menor unidade é o *token*. Para o idioma inglês, um token é um intervalo contíguo de letras e números que não têm espaços ou pontuação.

Como os espaços e a pontuação não são tokens, concentre-se nas pistas de texto que você pode usar como recursos. Lembre-se de incluir variações de palavras, como:

* formulários do plural
* dezenas de verbos
* abreviações
* ortografia e erros de ortografia

Determine se o texto, porque ele distingue uma característica, tem de:

* Corresponder uma palavra ou frase exata: Considere adicionar uma entidade de expressão regular ou uma entidade de lista como um recurso para a entidade ou intenção.
* Corresponda a um conceito bem conhecido, como datas, horários ou nomes de pessoas: Use uma entidade predefinida como um recurso para a entidade ou intenção.
* Aprenda novos exemplos ao longo do tempo: Use uma lista de frases de alguns exemplos do conceito como um recurso para a entidade ou intenção.

## <a name="create-a-phrase-list-for-a-concept"></a>Criar uma lista de frases para um conceito

Uma lista de frases é uma lista de palavras ou frases que descreve um conceito. Uma lista de frases é aplicada como uma correspondência que não diferencia maiúsculas de minúsculas no nível do token.

Ao adicionar uma lista de frases, você pode definir o recurso como **[global](#global-features)**. Um recurso global se aplica a todo o aplicativo.

### <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Use uma lista de frases quando precisar que seu aplicativo LUIS para generalizar e identificar novos itens para o conceito. As listas de frases são como vocabulário específico de domínio. Eles aprimoram a qualidade da compreensão para intenções e entidades.

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

Com uma lista de frases, LUIS considera contexto e generalizações para identificar itens semelhantes a, mas não é uma correspondência exata de texto. Siga estas etapas para usar uma lista de frases:

1. Comece com uma entidade de aprendizado de máquina:
    1. Adicione o exemplo declarações.
    1. Rótulo com uma entidade de aprendizado de máquina.
1. Adicionar uma lista de frases:
    1. Adicione palavras com significado semelhante. Não adicione todas as palavras ou frases possíveis. Em vez disso, adicione algumas palavras ou frases de cada vez. Em seguida, retreine e publique.
    1. Revise e adicione palavras sugeridas.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Um cenário típico para uma lista de frases

Um cenário típico para uma lista de frases é impulsionar palavras relacionadas a uma ideia específica.

Os termos médicos são um bom exemplo de palavras que podem precisar de uma lista de frases para aumentar sua importância. Esses termos podem ter significados físicos, químicos, Therapeutic ou abstratos específicos. O LUIS não saberá que os termos são importantes para o domínio do assunto sem uma lista de frases.

Para extrair os termos médicos:

1. Crie exemplos declarações e Rotule os termos médicos dentro desses declarações.
2. Crie uma lista de frases com exemplos dos termos dentro do domínio do assunto. Essa lista de frases deve incluir o termo real rotulado e outros termos que descrevam o mesmo conceito.
3. Adicione a lista de frases à entidade ou subentidade que extrai o conceito usado na lista de frases. O cenário mais comum é um componente (filho) de uma entidade de aprendizado de máquina. Se a lista de frases deve ser aplicada em todas as intenções ou entidades, marque a lista de frases como uma lista de frases globais. O sinalizador **enabledForAllModels** controla esse escopo de modelo na API.

### <a name="token-matches-for-a-phrase-list"></a>Correspondências de token para uma lista de frases

Uma lista de frases sempre se aplica ao nível do token. A tabela a seguir mostra como uma lista de frases que tem a palavra **Ana** se aplica a variações dos mesmos caracteres nessa ordem.


| Variação de token de **Ana** | A lista de frases corresponde quando o token é encontrado |
|--------------------------|---------------------------------------|
| **Maria**<br>**Maria**<br>           | Sim-o token é **Ana**                  |
| **Ana Maria**                    | Sim-o token é **Ana**                  |
| **Anne**                     | No-token é **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Um modelo como um recurso ajuda outro modelo

Você pode adicionar um modelo (intenção ou entidade) como um recurso a outro modelo (intenção ou entidade). Ao adicionar uma entidade ou uma intenção existente como um recurso, você está adicionando um conceito bem definido que tem exemplos rotulados.

Ao adicionar um modelo como um recurso, você pode definir o recurso como:
* **[Necessário](#required-features)**. Um recurso necessário deve ser encontrado para que o modelo seja retornado do ponto de extremidade de previsão.
* **[Global](#global-features)**. Um recurso global se aplica a todo o aplicativo.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quando usar uma entidade como um recurso para uma intenção

Adicione uma entidade como um recurso a uma intenção quando a detecção dessa entidade for significativa para a intenção.

Por exemplo, se a intenção for reservar um vôo, como **BookFlight**, e a entidade for uma informação de tíquete (como o número de estações, origem e destino), localizar a entidade de informações de tíquete deve adicionar um peso significativo à previsão da intenção de **BookFlight** .

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quando usar uma entidade como um recurso para outra entidade

Uma entidade (A) deve ser adicionada como um recurso a outra entidade (B) quando a detecção dessa entidade (A) é significativa para a previsão da entidade (B).

Por exemplo, se uma entidade de endereço de envio estiver contida em uma subentidade de endereço de rua, a subentidade de endereço adicionará um peso significativo à previsão para a entidade endereço de envio.

* Endereço de envio (entidade de aprendizado de máquina):

    * Número da rua (subentidade)
    * Endereço (subentidade)
    * Cidade (subentidade)
    * Estado ou província (subentidade)
    * País/região (subentidade)
    * CEP (subentidade)

## <a name="nested-subentities-with-features"></a>Subentidades aninhadas com recursos

Uma subentidade de aprendizado de máquina indica que um conceito está presente na entidade pai. O pai pode ser outra subentidade ou a entidade superior. O valor da subentidade atua como um recurso para seu pai.

Uma subentidade pode ter uma lista de frases e um modelo (outra entidade) como um recurso.

Quando a subentidade tem uma lista de frases, ela aumenta o vocabulário do conceito, mas não adiciona nenhuma informação à resposta JSON da previsão.

Quando a subentidade tem um recurso de outra entidade, a resposta JSON inclui os dados extraídos dessa outra entidade.


## <a name="required-features"></a>Recursos necessários

Um recurso necessário deve ser encontrado para que o modelo seja retornado do ponto de extremidade de previsão. Use um recurso necessário quando você souber que seus dados de entrada devem corresponder ao recurso.

Se o texto expressão não corresponder ao recurso necessário, ele não será extraído.

Um recurso necessário usa uma entidade de aprendizagem que não é de máquina:

* Entidade de expressão regular
* Entidade de lista
* Entidade predefinida

Se você tiver certeza de que seu modelo será encontrado nos dados, defina o recurso conforme necessário. Um recurso necessário não retornará nada se não for encontrado.

Continuando com o exemplo do endereço de envio:

Endereço de envio (entidade aprendida pelo computador)

 * Número da rua (subentidade)
 * Endereço (subentidade)
 * Nome da rua (subentidade)
 * Cidade (subentidade)
 * Estado ou província (subentidade)
 * País/região (subentidade)
 * CEP (subentidade)

### <a name="required-feature-using-prebuilt-entities"></a>Recurso necessário usando entidades predefinidas

A cidade, o estado e o país/região geralmente são um conjunto fechado de listas, o que significa que elas não mudam muito ao longo do tempo. Essas entidades podem ter os recursos recomendados relevantes e esses recursos podem ser marcados como obrigatórios. Isso significa que o endereço de envio inteiro não será retornado se as entidades que têm os recursos necessários não forem encontradas.

E se a cidade, o estado ou o país/região estiverem no expressão, mas estiverem em um local ou se gíriasrem que o LUIS não espera? Se você quiser fornecer algum pós-processamento para ajudar a resolver a entidade, devido a uma pontuação de baixa confiança do LUIS, não marque o recurso conforme necessário.

Outro exemplo de um recurso necessário para o endereço de envio é tornar o número da rua um número necessário, [predefinido](luis-reference-prebuilt-entities.md) . Isso permite que um usuário insira "1 Microsoft Way" ou "One Microsoft Way". Ambos resolvem o numeral "1" para a subentidade de número de rua.

### <a name="required-feature-using-list-entities"></a>Recurso necessário usando entidades de lista

Uma [entidade de lista](reference-entity-list.md) é usada como uma lista de nomes canônicos junto com seus sinônimos. Como um recurso necessário, se o expressão não incluir o nome canônico ou sinônimo, a entidade não será retornada como parte do ponto de extremidade de previsão.

Suponha que sua empresa seja enviada apenas para um conjunto limitado de países/regiões. Você pode criar uma entidade de lista que inclui várias maneiras para o cliente referenciar o país/região. Se LUIS não encontrar uma correspondência exata dentro do texto do expressão, a entidade (que tem o recurso necessário da entidade List) não será retornada na previsão.

|Nome canônico|Sinônimos|
|--|--|
|Estados Unidos|EUA<br>U. S. A<br>EUA<br>EUA<br>0|

Um aplicativo cliente, como um bot de bate-papo, pode fazer uma pergunta de acompanhamento para ajudar. Isso ajuda o cliente a entender que a seleção de país/região é limitada e *necessária*.

### <a name="required-feature-using-regular-expression-entities"></a>Recurso necessário usando entidades de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) usada como um recurso necessário fornece recursos avançados de correspondência de texto.

No exemplo de endereço de envio, você pode criar uma expressão regular que captura regras de sintaxe dos códigos postais de país/região.

## <a name="global-features"></a>Recursos globais

Embora o uso mais comum seja aplicar um recurso a um modelo específico, você pode configurar o recurso como um **recurso global** para aplicá-lo a todo o seu aplicativo.

O uso mais comum de um recurso global é adicionar um vocabulário adicional ao aplicativo. Por exemplo, se os clientes usam um idioma principal, mas esperam poder usar outro idioma dentro do mesmo expressão, você pode adicionar um recurso que inclui palavras do idioma secundário.

Como o usuário espera usar o idioma secundário em qualquer tentativa ou entidade, adicione palavras do idioma secundário à lista de frases. Configure a lista de frases como um recurso global.

## <a name="combine-features-for-added-benefit"></a>Combine recursos para obter benefícios adicionais

Você pode usar mais de um recurso para descrever uma característica ou um conceito. Um emparelhamento comum é usar:

* Um recurso de lista de frases: você pode usar várias listas de frases como recursos para o mesmo modelo.
* Um modelo como um recurso: [entidade predefinida](luis-reference-prebuilt-entities.md), [entidade de expressão regular](reference-entity-regular-expression.md), [entidade de lista](reference-entity-list.md). 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>Exemplo: recursos de entidade de escrituração de tíquete para um aplicativo de viagem  

Como um exemplo básico, considere um aplicativo para reservar um vôo com uma _tentativa_ de reserva de voo e uma _entidade_de escrituração de tíquetes. A entidade de escrituração de tíquetes captura as informações para registrar um bilhete de avião em um sistema de reserva. 

A entidade de aprendizado de máquina para o catálogo de tíquetes tem duas subentidades para capturar a origem e o destino. Os recursos precisam ser adicionados a cada subentidade, não à entidade de nível superior.

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Esquema de entidade Ticketbooking":::

A entidade de reserva de tíquetes é uma entidade de aprendizado de máquina, com subentidades, incluindo _origem_ e _destino_. Essas subentidades indicam uma localização geográfica. Para ajudar a extrair os locais e distinguir entre _origem_ e _destino_, cada subentidade deve ter recursos.

|Type|Subentidade de origem |Subentidade de destino|
|--|--|--|
|Modelo como um recurso|entidade predefinida [geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3)|entidade predefinida [geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3)|
|Lista de frases|**Palavras de origem**: `start at` , `begin from` , `leave`|**Palavras de destino**:,,,,, `to` `arrive` `land at` `go` `going` `stay` , `heading`|
|Lista de frases|Códigos de aeroporto – a mesma lista para a origem e o destino|Códigos de aeroporto – a mesma lista para a origem e o destino|
|Lista de frases|Nomes de aeroportos – a mesma lista para a origem e o destino|Códigos de aeroporto – a mesma lista para a origem e o destino|

Se você antecipar que as pessoas usam códigos de aeroportos e nomes de aeroportos, o LUIS deve ter listas de frases que usam os dois tipos de frases. Os códigos de aeroportos podem ser mais comuns com o texto inserido em um chatbot, enquanto os nomes de aeroportos podem ser mais comuns com uma conversa falada, como um chatbot habilitado para fala.

Os detalhes de correspondência dos recursos são retornados somente para modelos, não para listas de frases porque apenas modelos são retornados em JSON de previsão.

#### <a name="ticket-booking-labeling-in-the-intent"></a>Tíquete-marcação de concessão na intenção

Depois de criar a entidade de aprendizado de máquina, você precisará adicionar o exemplo declarações a uma intenção e rotular a entidade pai e todas as subentidades.

Para o exemplo de reserva de tíquetes, Rotule o exemplo declarações na intenção com a `TicketBooking` entidade e todas as subentidades no texto.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Esquema de entidade Ticketbooking":::

### <a name="example-pizza-ordering-app"></a>Exemplo: aplicativo de pedido de pizza

Para um segundo exemplo, considere um aplicativo para um restaurante de pizza, que recebe pedidos de pizza, incluindo os detalhes do tipo de pizza que alguém está solicitando. Cada detalhe da pizza deve ser extraído, se possível, para concluir o processamento do pedido.

A entidade de aprendizado de máquina neste exemplo é mais complexa com subentidades aninhadas, listas de frases, entidades predefinidas e entidades personalizadas.

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Esquema de entidade Ticketbooking":::

Este exemplo usa recursos no nível de subentidade e o filho do nível de subentidade. O nível que obtém o tipo de lista de frases ou modelo como um recurso é uma parte importante do design de sua entidade.

Embora as subentidades possam ter muitas listas de frases como recursos que ajudam a detectar a entidade, cada uma delas tem apenas um modelo como um recurso. Neste [aplicativo de pizza](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json), esses modelos são listados principalmente.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Esquema de entidade Ticketbooking":::

O exemplo rotulado corretamente declarações é exibido de forma a mostrar como as entidades são aninhadas. 


## <a name="best-practices"></a>Práticas recomendadas

Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

* [Estenda](schema-change-prediction-runtime.md) seus modelos de aplicativo no tempo de execução de previsão.
* Consulte [Adicionar recursos](luis-how-to-add-features.md) para saber mais sobre como adicionar recursos ao seu aplicativo Luis.
