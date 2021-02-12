---
title: Perguntas frequentes sobre assistentes de voz
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas mais populares sobre assistentes de voz usando comandos personalizados ou canal de fala de linha direta.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 4b0bbb982ed48dc052b1a15514ad36b1d69b62b5
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599421"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Perguntas frequentes sobre assistentes de voz

Se você não encontrar respostas para suas perguntas neste documento, confira [outras opções de suporte](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Geral

**P: o que é um assistente de voz?**

**R:** Como o Cortana, um assistente de voz é uma solução que escuta o declarações falado de um usuário, analisa o conteúdo desses declarações para saber, executa uma ou mais ações em resposta à intenção do expressão e, em seguida, fornece uma resposta ao usuário que geralmente inclui um componente falado. É uma experiência de "entrada de voz e saída" para interagir com um sistema. os autores do assistente de voz criam um aplicativo no dispositivo usando o `DialogServiceConnector` no SDK de fala para se comunicar com um assistente criado usando [comandos personalizados](custom-commands.md) ou o canal de [fala de linha direta](direct-line-speech.md) da estrutura de bot. Esses assistentes podem usar palavras-chave personalizadas, fala personalizada e voz personalizada para fornecer uma experiência adaptada à sua marca ou produto.

**P: devo usar comandos personalizados ou a fala de linha direta? Qual é a diferença?**

**R:** os [comandos personalizados](custom-commands.md) são um conjunto de ferramentas de menor complexidade para criar e hospedar facilmente um assistente que é adequado para cenários de conclusão de tarefas. A [Direct line Speech](direct-line-speech.md) fornece recursos mais avançados e mais sofisticados que podem permitir cenários de conversação robustos. Consulte a [comparação de soluções do assistente](voice-assistants.md#choosing-an-assistant-solution) para obter mais informações.

**P: Como faço para começar?**

**R:** A melhor maneira de começar a criar um aplicativo de comandos personalizados (versão prévia) ou bot de estrutura de bot básico.

- [Criar um aplicativo de comandos personalizados (versão prévia)](./quickstart-custom-commands-application.md)
- [Criar um bot de estrutura de bot básico](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Conectar um bot ao canal de fala de linha direta](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Depuração

**P: onde está meu segredo de canal?**

**R:** Se você tiver usado a versão de visualização da fala de linha direta ou estiver lendo a documentação relacionada, poderá esperar encontrar uma chave secreta na página de registro do canal de fala de linha direta. O método de `DialogServiceConfig` fábrica v 1.7 `FromBotSecret` no SDK de fala também espera esse valor.

A versão mais recente da Direct line Speech simplifica o processo de contato do bot a partir de um dispositivo. Na página de registro do canal, a lista suspensa na parte superior associa seu registro de canal de fala de linha direta a um recurso de fala. Uma vez associado, o SDK de fala v 1.8 inclui um `BotFrameworkConfig::FromSubscription` método de fábrica que irá configurar um `DialogServiceConnector` para contatar o bot que você associou à sua assinatura.

Se você ainda estiver migrando seu aplicativo cliente de v 1.7 para v 1.8, o `DialogServiceConfig::FromBotSecret` pode continuar a trabalhar com um valor não vazio e não nulo para seu parâmetro secreto de canal, por exemplo, o segredo anterior que você usou. Ele simplesmente será ignorado ao usar uma assinatura de fala associada a um registro de canal mais recente. Observe que o valor _deve_ ser não nulo e não vazio, pois eles são verificados no dispositivo antes que a associação do lado do serviço seja relevante.

Para obter um guia mais detalhado, consulte a [seção do tutorial](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) que percorre o registro do canal.

**P: recebo um erro 401 ao se conectar e nada funciona. Sei que minha chave de assinatura de fala é válida. O que está acontecendo?**

**R:** Ao gerenciar sua assinatura no portal do Azure, verifique se você está usando o recurso de **fala** (Microsoft. CognitiveServicesSpeechServices, "fala") e _não_ o recurso de **Serviços cognitivas** (Microsoft. CognitiveServicesAllInOne, "todos os serviços cognitivas"). Além disso, verifique o [suporte de região do serviço de fala para assistentes de voz](regions.md#voice-assistants).

![assinatura correta para a Direct line Speech](media/voice-assistants/faq-supported-subscription.png "exemplo de uma assinatura de fala compatível")

**P: obtenho o texto de reconhecimento do meu `DialogServiceConnector` , mas vejo um erro ' 1011 ' e nada do meu bot. Por?**

**R:** Esse erro indica um problema de comunicação entre o assistente e o serviço de assistente de voz.

- Para comandos personalizados, verifique se o aplicativo de comandos personalizados está publicado
- Para a fala de linha direta, verifique se você [conectou o bot ao canal de fala de linha direta](/azure/bot-service/bot-service-channel-connect-directlinespeech), [adicionou suporte de protocolo de streaming](/azure/bot-service/directline-speech-bot) ao bot (com o suporte de soquete da Web relacionado) e, em seguida, verifique se o bot está respondendo às solicitações de entrada do canal.

**P: esse código ainda não funciona e/ou estou recebendo um erro diferente ao usar um `DialogServiceConnector` . O que devo fazer?**

**R:** O log baseado em arquivo fornece substancialmente mais detalhes e pode ajudar a acelerar as solicitações de suporte. Para habilitar essa funcionalidade, consulte [como usar o log de arquivos](how-to-use-logging.md).

## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas](troubleshooting.md)
- [Notas sobre a versão](releasenotes.md)