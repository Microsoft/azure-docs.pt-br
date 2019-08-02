---
title: Planejar seu aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Descreva intenções e entidades de aplicativo relevantes e crie seus planos de aplicativo no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dc648b30dc1236080be06044f510557ae0ce9476
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638301"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planeje seu aplicativo LUIS com o domínio do assunto, intenções e entidades

Para planejar seu aplicativo, identifique seu domínio de área de assunto. Isso inclui possíveis tentativas e entidades que são relevantes para seu aplicativo.  

## <a name="identify-your-domain"></a>Identificar seu domínio

Um aplicativo LUIS gira em torno de um tópico específico do domínio.  Por exemplo, você pode ter um aplicativo de viagem que executa a reserva de tíquetes, voos, hotéis e carros de aluguel. Outro aplicativo pode fornecer conteúdo relacionado a exercícios, acompanhamento de esforços fitness e definição de metas. Identifica o domínio ajuda a localizar palavras ou frases que são importantes para seu domínio.

> [!TIP]
> O LUIS oferece [domínios predefinidos](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns.
> Verifique para ver se é possível usar um domínio predefinido como ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções

Pense nas [intenções](luis-concept-intent.md) importantes para a tarefa do seu aplicativo. Vamos usar o exemplo de um aplicativo de viagem, com funções para reservar um voo e verificar o clima no destino do usuário. É possível definir as intenções "BookFlight" e "GetWeather" para essas ações. Em um aplicativo mais complexo com mais funções, você tem mais intenções e deve defini-las com cuidado para não serem muito específicas. Por exemplo, "BookFlight" e "BookHotel" podem precisar ser intenções separadas, mas "BookInternationalFlight" e "BookDomesticFlight" podem ser muito semelhantes.

> [!NOTE]
> É uma melhor prática usar apenas as intenções de que você precisar para executar as funções do seu aplicativo. Se você definir intenções demais, ficará mais difícil para o LUIS classificar declarações corretamente. Se você definir poucas demais, elas podem ser tão genéricas que se sobrepõem.

## <a name="create-example-utterances-for-each-intent"></a>Crie exemplos de declarações para cada intenção

Depois de determinar as tentativas, crie um exemplo de 15 a 30 declarações para cada tentativa. Para começar, não tem menos que esse número ou crie muitas declarações para cada tentativa. Cada enunciado deve ser diferente do enunciado anterior. Uma boa variedade nos enunciados inclui contagem global de palavras, escolha de palavras, tempo verbal e pontuação. 

Examine [declarações](luis-concept-utterance.md) para obter mais informações.

## <a name="identify-your-entities"></a>Identificar suas entidades

Nos exemplos de declarações, identifique as entidades que você deseja extrair. Para reservar um vôo, você precisa de informações como o destino, a data, a companhia aérea, a categoria de tíquete e a classe de viagem. Crie entidades para esses tipos de dados e marque as [entidades](luis-concept-entity-types.md) no exemplo declarações porque elas são importantes para realizar uma intenção. 

Quando você determina quais entidades usar em seu aplicativo, tenha em mente que há diferentes tipos de entidades para capturar relacionamentos entre tipos de objetos. [Entidades no LUIS](luis-concept-entity-types.md) oferece mais detalhes sobre os tipos diferentes.

## <a name="next-steps"></a>Próximas etapas

Depois que seu aplicativo for treinado, publicado e receber declarações de ponto final, planeje implementar melhorias de previsão com [aprendizado ativo](luis-how-to-review-endpoint-utterances.md), [listas de frases](luis-concept-feature.md) e [padrões](luis-concept-patterns.md). 


* Consulte [Criar o primeiro aplicativo de LUIS (Serviço Inteligente de Reconhecimento Vocal)](luis-get-started-create-app.md) para obter um breve passo a passo sobre como criar um aplicativo de LUIS.
