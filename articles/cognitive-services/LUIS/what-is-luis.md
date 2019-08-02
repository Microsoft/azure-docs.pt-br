---
title: O que é o Reconhecimento Vocal (LUIS) - Serviços Cognitivos da Microsoft | Microsoft Docs
description: O Reconhecimento Vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 569b33d299f52f0da50d8a8992420754aa85b533
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062161"
---
# <a name="what-is-language-understanding-luis"></a>O que é Reconhecimento Vocal (LUIS)?

O Reconhecimento Vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes. 

Um aplicativo cliente para LUIS é qualquer aplicativo que se comunica com um usuário em linguagem natural para completar uma tarefa. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots e aplicativos para área de trabalho habilitados para fala.  

![Imagem conceitual de 3 aplicativos clientes trabalhando com Reconhecimento Vocal (LUIS) dos Serviços Cognitivos](./media/luis-overview/luis-entry-point.png "Imagem conceitual de 3 aplicativos clientes trabalhando com Reconhecimento Vocal (LUIS) dos Serviços Cognitivos")

## <a name="use-luis-in-a-chat-bot"></a>Usar o LUIS em um chatbot

<a name="Accessing-LUIS"></a>

Depois que seu aplicativo LUIS for publicado, seu aplicativo cliente envia expressões (texto) para a [API][endpoint-apis] do ponto de extremidade de processamento da linguagem natural LUIS e recebe os resultados como respostas em JSON. Um aplicativo cliente comum para LUIS é um chatbot.


![Imagens conceituais do LUIS trabalhando com o Chatbot para prever o texto do usuário com reconhecimento de linguagem natural (NLP)](./media/luis-overview/luis-overview-process-2.png "Imagens conceituais do LUIS trabalhando com o Chatbot para prever o texto do usuário com reconhecimento de linguagem natural (NLP")

|Etapa|Ação|
|:--|:--|
|1|O aplicativo cliente envia a _expressão_ do usuário (texto em suas próprias palavras), “Eu quero chamar meu representante de RH”. para o ponto de extremidade do LUIS como uma solicitação HTTP.|
|2|O LUIS aplica o modelo aprendido no texto de linguagem natural para fornecer um reconhecimento inteligente sobre a entrada do usuário. O LUIS retorna uma resposta formatada em JSON, com uma intenção principal, “ContatoRH”. A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade do Tipo de contato.|
|3|O aplicativo cliente usa a resposta JSON para tomar decisões sobre como atender às solicitações do usuário. Essas decisões podem incluir algumas árvores de decisão no código do Bot Framework e chamadas para outros serviços. |

O aplicativo LUIS fornece inteligência, para que o aplicativo cliente possa fazer escolhas inteligentes. O LUIS não oferece essas escolhas. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Processamento de idioma natural

Um aplicativo LUIS contém um modelo de linguagem natural de domínio específico. Você pode iniciar o aplicativo LUIS com um modelo de domínio predefinido, criar seu próprio modelo ou combinar partes de um domínio predefinido com suas próprias informações personalizadas.

* **Modelo predefinido** LUIS tem muitos modelos de domínio que incluem intenções, expressões e entidades predefinidas. Você pode usar as entidades predefinidas sem a necessidade de usar as intenções e expressões do modelo predefinido. [Modelos de domínio predefinidos](luis-how-to-use-prebuilt-domains.md) incluem todo o design para você e são uma ótima maneira de começar a usar o LUIS rapidamente.

* **Entidades personalizadas** LUIS oferece várias maneiras de identificar suas próprias intenções e entidades personalizadas incluindo entidades de aprendizado de máquina, entidades específicas ou literais e uma combinação de literais e aprendizado de máquina.

## <a name="build-the-luis-model"></a>Criar o modelo LUIS
Criar o modelo com as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087) ou com o portal do LUIS.

O modelo LUIS começa com categorias de intenções de usuário chamadas **[intenções](luis-concept-intent.md)** . Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do usuário. Cada expressão pode fornecer vários dados que precisam ser extraídos com **[entidades](luis-concept-entity-types.md)** . 

|Expressão de usuário de exemplo|Intenção|Entidades|
|-----------|-----------|-----------|
|"Reservar um voo para __Seattle__?"|BookFlight|Seattle|
|"Quando a sua loja é __aberta__?"|StoreHoursAndLocation|aberta|
|"Agendar uma reunião às __13h__ com __Bob__ na distribuição"|ScheduleMeeting|13h, Bob|

## <a name="query-prediction-endpoint"></a>Ponto de extremidade de previsão da consulta

Depois que o modelo é criado e publicado no ponto de extremidade, o aplicativo cliente envia expressões à API de [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) da previsão publicada. A API aplica o modelo ao texto para análise. A API responde com os resultados de previsão em um formato JSON.  

A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade do **Tipo de contato** seguinte. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Melhorar a previsão do modelo

Depois que um modelo LUIS é publicado e recebe expressões de usuário real, LUIS fornece vários métodos para aumentar a precisão de previsão: [aprendizado ativo](luis-concept-review-endpoint-utterances.md) de expressões de ponto de extremidade, [listas de frases](luis-concept-feature.md) para inclusão de palavra de domínio, e [padrões](luis-concept-patterns.md) para reduzir o número de expressões necessárias.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento
O LUIS fornece ferramentas, controle de versão e colaboração com outros autores do LUIS para integrar o ciclo de vida de desenvolvimento completo no nível do aplicativo cliente e do modelo de linguagem. 

## <a name="implementing-luis"></a>Implementando o LUIS
O LUIS, como uma API REST, pode ser usado com qualquer produto, serviço ou estrutura que faz uma solicitação HTTP. A lista a seguir contém os principais produtos e serviços da Microsoft usados com o LUIS.

O principal aplicativo cliente para o LUIS é:
* [Bot de aplicativo Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) que cria rapidamente um chatbot habilitado para LUIS para conversar com um usuário por meio de entrada de texto. Usa o [Bot Framework][bot-framework] versão [4.x](https://github.com/Microsoft/botbuilder-dotnet) para uma experiência completa de bot.

Ferramentas para usar o LUIS com um bot de modo rápido e fácil:
* [CLI do LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) O pacote NPM fornece criação e previsão como uma ferramenta de linha de comando autônoma ou como uma importação. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) O LUISGen é uma ferramenta para gerar código-fonte typescript e C# fortemente tipado de um modelo do LUIS exportado.
* [Expedição](https://aka.ms/dispatch-tool) permite que vários aplicativos LUIS e QnA Maker sejam usados em um aplicativo pai usando o modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown é uma ferramenta de linha de comando que ajuda a gerenciar modelos de linguagem para o bot.

Outros Serviços Cognitivos usados com o LUIS:
* [QnA Maker][qnamaker] permite que vários tipos de texto sejam combinados em uma base de conhecimento de perguntas e respostas.
* [API de Verificação Ortográfica do Bing](../bing-spell-check/proof-text.md) fornece correção de texto antes da previsão. 
* [Serviço de Fala](../Speech-Service/overview.md) converte as solicitações de linguagem falada em texto. 
* [Aprendiz de conversa](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) permite que você crie conversas de bot mais rapidamente com o LUIS.
* [Bate-papo de personalidade do projeto](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) para lidar com conversa informais do bot.

Exemplos usando o LUIS:
* Repositório GitHub de [Inteligência Artificial de conversação](https://github.com/Microsoft/AI).
* Exemplos do Azure de [Reconhecimento Vocal](https://github.com/Azure-Samples/cognitive-services-language-understanding)

## <a name="next-steps"></a>Próximas etapas

Crie um novo aplicativo LUIS com um domínio [predefinido](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md). [Consulte o ponto de extremidade de previsão](luis-get-started-cs-get-intent.md) de um aplicativo de IoT público.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
