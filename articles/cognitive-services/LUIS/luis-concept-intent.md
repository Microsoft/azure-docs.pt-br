---
title: Intenções e entidades-LUIS
titleSuffix: Azure Cognitive Services
description: Uma única intenção representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou uma meta expressa no enunciado de um usuário. Defina um conjunto de intenções que corresponda às ações que os usuários desejem executar em seu aplicativo.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025901"
---
# <a name="intents-in-your-luis-app"></a>Tentativas em seu aplicativo LUIS

Uma intenção representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou meta expressa na [declaração](luis-concept-utterance.md) de um usuário.

Defina um conjunto de intenções que corresponda às ações que os usuários desejem executar em seu aplicativo. Por exemplo, um aplicativo de viagem define várias intenções:

Intenções do aplicativo de viagem   |   Exemplo de enunciados   |
------|------|
 BookFlight     |   "Reserve um voo para mim para o Rio na semana que vem" <br/> "Coloque-me num voo para o Rio no dia 24" <br/> "Preciso de uma passagem de avião no próximo domingo para o Rio de Janeiro"    |
 Saudação     |   "Oi" <br/>"Olá" <br/>"Bom dia"  |
 CheckWeather | "Como está o clima em Boston?" <br/> "Mostre-me a previsão para este fim de semana" |
 Nenhum         | "Encontre uma receita de biscoitos para mim"<br>"Os Lakers venceram?" |

Todos os aplicativos vêm com a intenção predefinida, "[None](#none-intent)", que é a tentativa de fallback.

## <a name="prebuilt-domains-provide-intents"></a>Domínios predefinidos fornecem intenções
Além de tentativas que você define, você pode usar tentativas predefinidas de um dos [domínios predefinidos](./howto-add-prebuilt-models.md).

## <a name="return-all-intents-scores"></a>Retornar pontuações de todas as intenções
Atribua uma declaração a uma única intenção. Quando LUIS recebe um expressão no ponto de extremidade, por padrão, ele retorna a principal intenção desse expressão.

Se você quiser as pontuações para todas as intenções para o expressão, poderá fornecer um sinalizador na cadeia de caracteres de consulta da API de previsão.

|Versão da API de previsão|Sinalizador|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intenção comparada com a entidade
A intenção representa a ação que o aplicativo deve executar para o usuário e é baseado em todo o expressão. Uma declaração pode ter apenas uma intenção de pontuação principal, mas pode ter muitas entidades.

<a name="how-do-intents-relate-to-entities"></a>

 Crie uma intenção quando a _intenção_ do usuário disparar uma ação em seu aplicativo cliente, como uma chamada à função checkweather(). Em seguida, crie entidades para representar os parâmetros necessários para executar a ação.

|Intencional   | Entidade | Exemplo de enunciado   |
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Qual é o clima como em `Seattle` `tomorrow` ? |
| CheckWeather | { "type": "date_range", "entity": "this weekend" } | Mostre-me a previsão para `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Intenções de domínio predefinidas

[Domínios pré-criados](./howto-add-prebuilt-models.md) fornecem intenções com declarações.

## <a name="none-intent"></a>Intenção None

A intenção **None** é criada, mas deixada vazia de propósito. A tentativa **None** é uma intenção necessária e não pode ser excluída ou renomeada. Preencha-a com declarações que estejam fora de seu domínio.

A intenção **None** é a intenção de fallback, importante em todos os aplicativos e deve ter 10% do total de declarações. Ela é usada para ensinar ao LUIS declarações que não são importantes no domínio de aplicativo (área de assunto). Se você não adicionar nenhuma declaração para a intenção **None**, o LUIS forçará uma declaração que está fora do domínio para uma das intenções do domínio. Isso distorcerá as pontuações de previsão ensinando ao LUIS a intenção incorreta para a declaração.

Quando um expressão é previsto como a intenção None, o aplicativo cliente pode fazer mais perguntas ou fornecer um menu para direcionar o usuário a opções válidas.

## <a name="negative-intentions"></a>Intenções negativas
Se desejar determinar intenções negativas e positivas, como "Eu **quero** um carro" e "Eu **não** quero um carro", será possível criar duas intenções (uma positiva e uma negativa) e adicionar declarações adequadas para cada uma. Ou é possível criar uma única intenção e marcar os dois termos positivos e negativos diferentes como uma entidade.

## <a name="intents-and-patterns"></a>Tentativas e padrões

Se você tiver um exemplo de declarações, que pode ser definido em parte ou inteiro como uma expressão regular, considere usar a [entidade de expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhada com um [padrão](luis-concept-patterns.md).

O uso de uma entidade de expressão regular garante a extração de dados para que o padrão seja correspondido. O padrão de correspondência garante que uma intenção exata seja retornada.

## <a name="intent-balance"></a>Equilíbrio de intenções
As intenções do domínio de aplicativo devem ter um equilíbrio de declarações em cada intenção. Não tenha uma intenção com 10 declarações e outra intenção com 500 declarações. Isso não está equilibrado. Se você tiver essa situação, examine a intenção com 500 declarações para ver se muitas intenções podem ser reorganizadas em um [padrão](luis-concept-patterns.md).

A intenção **None** não está incluída no equilíbrio. A intenção deve conter 10% das declarações totais no aplicativo.

## <a name="intent-limits"></a>Limites de intenção
Examine os [limites](luis-limits.md#model-boundaries) para entender a quantidade de intenções que você pode adicionar a um modelo.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se precisar de mais do que o número máximo de intenções
Primeiro, considere se o sistema está usando um número excessivo de intenções.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Várias intenções poderão ser combinadas em uma única intenção com entidades
Intenções muito semelhantes poderão dificultar sua diferenciação pelo LUIS. As intenções devem ser variadas o suficiente para capturar as principais tarefas que o usuário está solicitando, mas elas não precisam capturar todo caminho que seu código usa. Por exemplo, BookFlight e FlightCustomerService podem ser intenções separadas em um aplicativo de viagem, mas BookInternationalFlight e BookDomesticFlight são semelhantes demais. Se o sistema precisar diferenciá-las, use entidades ou outra lógica, em vez de intenções.

### <a name="dispatcher-model"></a>Modelo de Dispatcher
Saiba como combinar aplicativos LUIS e QnA Maker com o [modelo de expedição](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Solicite ajuda para aplicativos com quantidades significativas de intenções
Se a redução do número de intenções ou a divisão das suas intenções em vários aplicativos não funcionar para você, contate o suporte. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [entidades](luis-concept-entity-types.md), que são palavras importantes relevantes para intenções
* Saiba como [adicionar e gerenciar intenções](luis-how-to-add-intents.md) em seu aplicativo LUIS.
* Examine as [melhores práticas](luis-concept-best-practices.md) da intenção