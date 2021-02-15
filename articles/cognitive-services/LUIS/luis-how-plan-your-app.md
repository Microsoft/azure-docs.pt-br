---
title: Planejar seu aplicativo-LUIS
description: Descreva intenções e entidades de aplicativo relevantes e crie seus planos de aplicativo no LUIS (Serviço Inteligente de Reconhecimento Vocal).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018949"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planejar o esquema do aplicativo LUIS com a extração de dados e domínio do assunto

Um esquema de aplicativo LUIS contém [intenções](luis-glossary.md#intent) e [entidades](luis-glossary.md#entity) relevantes para seu [domínio](luis-glossary.md#domain)de assunto. As intenções classificam [declarações](luis-glossary.md#utterance)do usuário e as entidades extraem dados do declarações do usuário.

## <a name="identify-your-domain"></a>Identificar seu domínio

Um aplicativo LUIS é centralizado em um domínio de assunto. Por exemplo, você pode ter um aplicativo de viagem que lide com a reserva de ingressos, vôos, hotéis e carros de aluguel. Outro aplicativo pode fornecer conteúdo relacionado a exercícios, acompanhamento de esforços fitness e definição de metas. Identificar o domínio ajuda a localizar palavras ou frases relevantes para seu domínio.

> [!TIP]
> O LUIS oferece [domínios predefinidos](./howto-add-prebuilt-models.md) para muitos cenários comuns. Verifique para ver se é possível usar um domínio predefinido como ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções

Pense nas [intenções](luis-concept-intent.md) que são importantes para a tarefa do seu aplicativo.

Vamos usar o exemplo de um aplicativo de viagem, com funções para reservar um voo e verificar o clima no destino do usuário. Você pode definir as `BookFlight` `GetWeather` tentativas e para essas ações.

Em um aplicativo mais complexo com mais funções, você tem mais intenções e deve defini-las com cuidado para que as intenções não sejam muito específicas. Por exemplo, `BookFlight` e `BookHotel` talvez precisem de tentativas separadas, mas `BookInternationalFlight` `BookDomesticFlight` pode ser muito semelhante.

> [!NOTE]
> É uma melhor prática usar apenas as intenções de que você precisar para executar as funções do seu aplicativo. Se você definir intenções demais, ficará mais difícil para o LUIS classificar declarações corretamente. Se você definir poucos, eles poderão ser tão gerais que se sobrepõem.

Se você não precisar identificar a intenção geral do usuário, adicione todos os declarações de usuário de exemplo à `None` intenção. Se seu aplicativo aumentar a necessidade de mais intenções, você poderá criá-los mais tarde.

## <a name="create-example-utterances-for-each-intent"></a>Crie exemplos de declarações para cada intenção

Para começar, Evite criar muitas declarações para cada tentativa. Depois de determinar as intenções, crie um exemplo de 15 a 30 declarações por tentativa. Cada expressão deve ser diferente do declarações fornecido anteriormente. Uma boa variedade no declarações inclui contagem geral de palavras, opções de palavras, conjugação de verbo e [Pontuação](luis-reference-application-settings.md#punctuation-normalization).

Para obter mais informações, consulte [Understanding Good declarações for Luis apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificar suas entidades

Nos exemplos de declarações, identifique as entidades que você deseja extrair. Para reservar um vôo, você precisa de informações como o destino, a data, a companhia aérea, a categoria de tíquete e a classe de viagem. Crie entidades para esses tipos de dados e marque as [entidades](luis-concept-entity-types.md) no exemplo declarações. As entidades são importantes para realizar uma intenção.

Ao determinar quais entidades usar em seu aplicativo, tenha em mente que existem diferentes tipos de entidades para capturar relações entre tipos de objeto. [Entidades no LUIS](luis-concept-entity-types.md) oferece mais detalhes sobre os tipos diferentes.

> [!TIP]
> O LUIS oferece [entidades predefinidas](./howto-add-prebuilt-models.md) para cenários de usuário comuns e de conversação. Considere o uso de entidades predefinidas como um ponto de partida para o desenvolvimento de aplicativos.

## <a name="resolution-with-intent-or-entity"></a>Resolução com intenção ou entidade?

Em muitos casos, especialmente ao trabalhar com conversa natural, os usuários fornecem um expressão que pode conter mais de uma função ou intenção. Para resolver isso, uma regra básica geral é entender que a representação da saída pode ser feita tanto em intenções quanto em entidades. Essa representação deve ser mapeada para suas ações de aplicativo cliente e não precisa ser limitada às intenções.

**Int-ent-Ties** é o conceito que as ações (geralmente compreendidas como intenções) também podem ser capturadas como entidades e confiadas nesse formulário no JSON de saída, onde você pode mapeá-la para uma ação específica. A _negação_ é um uso comum para aproveitar essa dependência em ambas as intenções e entidades para extração completa.

Considere as duas declarações a seguir, que são muito próximas Considerando a escolha do Word, mas têm resultados diferentes:

|Enunciado|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

Em vez de ter duas intenções separadas, crie uma única intenção com uma `FlightAction` entidade de aprendizado de máquina. A entidade de aprendizado de máquina deve extrair os detalhes da ação para um agendamento e uma solicitação de cancelamento, bem como um local de origem ou de destino.

A `FlightAction` entidade seria estruturada no seguinte pseudocódigo de entidades e subentidades de aprendizado de máquina:

* Vooaction
    * Ação
    * Origem
    * Destino

Para ajudar a extração, adicione recursos às subentidades. Você escolherá seus recursos com base no vocabulário que espera ver no usuário declarações e os valores que você deseja que sejam retornados na resposta de previsão.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprendendo o lifecylce de desenvolvimento do LUIS](luis-concept-app-iteration.md)