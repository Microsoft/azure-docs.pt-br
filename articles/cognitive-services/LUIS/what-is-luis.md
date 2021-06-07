---
title: O que é Reconhecimento Vocal (LUIS)?
description: 'LUIS (Reconhecimento vocal): um serviço de API baseado em nuvem que usa o machine learning em idioma natural e de conversa para prever o significado e extrair informações.'
keywords: Azure, inteligência artificial, ia, processamento de linguagem natural, nlp, compreensão de linguagem natural, nlu, LUIS, IA de conversação, chatbot de ia, ia do nlp, luis do azure
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 03/22/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f32f58bebc0a7d64443259981590e368b109b19b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278942"
---
# <a name="what-is-language-understanding-luis"></a>O que é Reconhecimento Vocal (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O LUIS (Reconhecimento Vocal) é um serviço de IA de conversação baseado em nuvem que aplica inteligência de machine learning personalizada em um texto de linguagem natural de conversação do usuário para prever o significado geral e extrair informações detalhadas relevantes.

Um aplicativo cliente para LUIS é qualquer aplicativo que se comunica com um usuário em linguagem natural para completar uma tarefa. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots de IA e aplicativos da área de trabalho habilitados para fala.

![Imagem conceitual de três aplicativos cliente trabalhando com Reconhecimento vocal de serviços cognitivos (LUIS)](./media/luis-overview/luis-entry-point.png "Imagem conceitual de três aplicativos cliente que trabalham com Reconhecimento vocal de serviços cognitivos (LUIS)")

Esta documentação contém os seguintes tipos de artigos:  

* Os [**guias de início rápido**](luis-get-started-create-app.md) são instruções de introdução que orientam sobre como fazer solicitações ao serviço.  
* Os [**guias de instruções**](luis-how-to-start-new-app.md) contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.  
* Os [**conceitos**](artificial-intelligence.md) fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.  
* Os [**tutoriais**](tutorial-intents-only.md) são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.  

## <a name="use-luis-in-a-chat-bot"></a>Usar o LUIS em um chatbot

<a name="Accessing-LUIS"></a>

Depois que o aplicativo LUIS do Azure for publicado, um aplicativo cliente envia enunciados (texto) para a [API][endpoint-apis] do ponto de extremidade de processamento da linguagem natural do LUIS e recebe os resultados como respostas em JSON. Um aplicativo cliente comum para LUIS é um chatbot.


![Imagens conceituais do LUIS trabalhando com o Chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Imagens conceituais do LUIS que trabalham com o Chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)")

|Etapa|Ação|
|:--|:--|
|1|O aplicativo cliente envia a _expressão_ do usuário (texto em suas próprias palavras), “Eu quero chamar meu representante de RH”. para o ponto de extremidade do LUIS como uma solicitação HTTP.|
|2|O LUIS permite que você crie seus modelos de linguagem personalizados para adicionar inteligência ao seu aplicativo. Os modelos de idioma aprendidos pela máquina pegam o texto de entrada não estruturado do usuário e retornam uma resposta formatada em JSON, com uma das principais intenções, `HRContact`. A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade do _Tipo de contato_.|
|3|O aplicativo cliente usa a resposta JSON para tomar decisões sobre como atender às solicitações do usuário. Essas decisões podem incluir algumas árvores de decisão no código do Bot Framework e chamadas para outros serviços. |

O aplicativo LUIS fornece inteligência, para que o aplicativo cliente possa fazer escolhas inteligentes. O LUIS não oferece essas escolhas.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>NLU (Reconhecimento de idioma natural)

O [LUIS fornece IA (inteligência artificial)](artificial-intelligence.md "O LUIS fornece IA (inteligência artificial)") na forma de NLU, um subconjunto da IA de processamento de linguagem natural.

Um aplicativo LUIS contém um modelo de linguagem natural de domínio específico. Você pode iniciar o aplicativo LUIS com um modelo de domínio predefinido, criar seu próprio modelo ou combinar partes de um domínio predefinido com suas próprias informações personalizadas.

* **Modelo predefinido** LUIS tem muitos modelos de domínio que incluem intenções, expressões e entidades predefinidas. Você pode usar as entidades predefinidas sem a necessidade de usar as intenções e expressões do modelo predefinido. [Modelos de domínio predefinidos](./howto-add-prebuilt-models.md "Modelos de domínio predefinidos") incluem todo o design para você e são uma ótima maneira de começar a usar o LUIS rapidamente.

* **O modelo personalizado** LUIS oferece várias maneiras de identificar seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades de aprendizado de máquina, entidades específicas ou literais e uma combinação de aprendizado de máquina e literal.

Saiba mais sobre a [IA do NLP](artificial-intelligence.md "NLP") e a área específica do LUIS do NLU.

## <a name="step-1-design-and-build-your-model"></a>Etapa 1: Projetar e criar seu modelo

Projete seu modelo com as categorias de intenções de usuário chamadas **[intenções](luis-concept-intent.md "intenções")** . Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md "enunciados")** do usuário. Cada enunciado pode fornecer dados que precisam ser extraídos com [entidades de machine learning](luis-concept-entity-types.md#effective-machine-learned-entities "entidades de machine learning").

|Expressão de usuário de exemplo|Intencional|Data da extração|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|Abrir|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|13h, Bob|

Crie o modelo com as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087 "criação"), o **[portal do LUIS](https://www.luis.ai "Portal do LUIS")** ou ambos. Saiba mais sobre como criar um modelo com o [portal](get-started-portal-build-app.md "portal") e as [bibliotecas de clientes do SDK](./client-libraries-rest-api.md?pivots=rest-api "Bibliotecas de cliente do SDK").

## <a name="step-2-get-the-query-prediction"></a>Etapa 2: Obter a previsão da consulta

Depois que o modelo do aplicativo é treinado e publicado no ponto de extremidade, um aplicativo cliente (como um chatbot) envia os enunciados à API do [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint") de previsão. A API aplica o modelo ao enunciado para análise e responde com os resultados da previsão em um formato JSON.

A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade e o sentimento geral do **Tipo de contato** seguinte.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Etapa 3: Melhorar a previsão do modelo

Depois que o aplicativo LUIS for publicado e receber expressões de usuário real, LUIS fornecerá [aprendizado ativo](luis-concept-review-endpoint-utterances.md "aprendizado ativo") de expressões do ponto de extremidade para melhorar a precisão da previsão. Examine essas sugestões como parte do seu trabalho de manutenção regular no ciclo de vida de desenvolvimento.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Ciclo de vida de desenvolvimento e ferramentas
O LUIS fornece ferramentas, controle de versão e colaboração com outros autores do LUIS para integrar ao [ciclo de vida de desenvolvimento completo](luis-concept-app-iteration.md "ciclo de vida do desenvolvimento").

O LUIS, como uma API REST, pode ser usado com qualquer produto, serviço ou estrutura que faz uma solicitação HTTP. O LUIS também fornece SDKs (bibliotecas de clientes) para várias das principais linguagens de programação. Saiba mais sobre os [recursos para desenvolvedor](developer-reference-resource.md "recursos para desenvolvedores") fornecidos.

Ferramentas para usar o LUIS com um bot de modo rápido e fácil:
* [CLI do LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "CLI d LUIS") O pacote NPM fornece criação e previsão como uma ferramenta de linha de comando autônoma ou como uma importação.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") O LUISGen é uma ferramenta para gerar código-fonte typescript e C# fortemente tipado de um modelo do LUIS exportado.
* [Expedição](https://aka.ms/dispatch-tool "Dispatch") permite que vários aplicativos LUIS e QnA Maker sejam usados em um aplicativo pai usando o modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") O LUDown é uma ferramenta de linha de comando que ajuda a gerenciar modelos de linguagem para o bot.

## <a name="integrate-with-a-bot"></a>Integração a um bot

Use o [Serviço de Bot do Azure](/azure/bot-service/ "Serviço de Bot do Azure") com o [Microsoft Bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") para criar e implantar um chatbot. Projete e desenvolva bots com a ferramenta de interface gráfica [Composer](/composer/ "Compositor") ou os [exemplos de bots funcionais](https://github.com/microsoft/BotBuilder-Samples "exemplos de bot de trabalho") projetados para os principais cenários de bots.

## <a name="integrate-with-other-cognitive-services"></a>Integração a outros Serviços Cognitivos

Outros Serviços Cognitivos usados com o LUIS:
* [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") permite que vários tipos de texto sejam combinados em uma base de conhecimento de perguntas e respostas.
* [Serviço de Fala](../Speech-Service/overview.md "Serviço de Fala") converte as solicitações de linguagem falada em texto.

O LUIS fornece a funcionalidade de Análise de Texto como parte dos recursos existentes. Essa funcionalidade inclui a [Análise de Sentimento](luis-how-to-publish-app.md#configuring-publish-settings "análise de sentimento") e a [Extração de Frases-chave](luis-reference-prebuilt-keyphrase.md "como extração de frases-chave") com a entidade keyPhrase predefinida.

## <a name="learn-with-the-quickstarts"></a>Saiba mais com os guias de início rápido

Saiba mais sobre o LUIS com guias de início rápido práticos usando o [portal](get-started-portal-build-app.md "portal") e as [bibliotecas de clientes do SDK](./client-libraries-rest-api.md?pivots=rest-api "bibliotecas de cliente do SDK").


## <a name="deploy-on-premises-using-docker-containers"></a>Implantação local usando contêineres do Docker

[Use os contêineres do LUIS](luis-container-howto.md) para implantar recursos de API no local. Esses contêineres do Docker permitem que você aproxime o serviço dos seus dados para fins de conformidade, segurança ou outras razões operacionais.

## <a name="next-steps"></a>Próximas etapas

* [Novidades](whats-new.md "Novidades") do serviço e da documentação
* [Planeje seu aplicativo](luis-how-plan-your-app.md "Planejar seu aplicativo") com [intenções](luis-concept-intent.md "intenções") e [entidades](luis-concept-entity-types.md "entidades").

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/