---
title: Conceitos empresariais-LUIS
titleSuffix: Azure Cognitive Services
description: Entenda os conceitos de design para aplicativos LUIS grandes ou em vários aplicativos, incluindo o LUIS e o QnA Maker juntos.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619928"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Estratégias empresariais para um aplicativo LUIS
Examine essas estratégias de design para seu aplicativo empresarial.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando você espera solicitações do LUIS além da cota

LUIS tem uma cota mensal, bem como uma cota por segundo, com base no tipo de preço do recurso do Azure. 

Se a taxa de solicitação do aplicativo LUIS exceder a [taxa de cota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)permitida, você poderá:

* Espalhe a carga para aplicativos mais LUIS com a [mesma definição de aplicativo](#use-multiple-apps-with-same-app-definition). Isso inclui, opcionalmente, a execução de LUIS de um [contêiner](luis-container-howto.md). 
* Crie e [atribua várias chaves](#assign-multiple-luis-keys-to-same-app) ao aplicativo. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Usar vários aplicativos com a mesma definição de aplicativo
Exporte o aplicativo LUIS original e, em seguida, importe-o de volta para aplicativos separados. Cada aplicativo tem sua própria ID do aplicativo. Quando você publica, em vez de usar a mesma chave entre todos os aplicativos, crie uma chave separada para cada aplicativo. Equilibre a carga entre todos os aplicativos para que nenhum aplicativo único fique sobrecarregado. Adicione o [Application Insights](luis-tutorial-bot-csharp-appinsights.md) para monitorar o uso. 

Para obter a mesma intenção principal entre todos os aplicativos, certifique-se de que a previsão da intenção entre a primeira e a segunda intenção seja ampla o suficiente para que o LUIS não fique confuso, oferecendo diferentes resultados entre aplicativos para obter pequenas variações em declarações. 

Ao treinar esses aplicativos irmãos, certifique-se de [treinar com todos os dados](luis-how-to-train.md#train-with-all-data).

Designe um único aplicativo como o principal. Quaisquer declarações sugeridas para exame devem ser adicionadas ao aplicativo principal e movidas de volta para todos os outros aplicativos. Isso é uma exportação completa do aplicativo ou o carregamento de declarações rotuladas do principal para os filhos. O carregamento pode ser feito a partir do site do [LUIS](luis-reference-regions.md) ou da API de criação para um [único enunciado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) ou para um [lote](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Agende uma revisão periódica, como a cada duas semanas, do [ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md) para o aprendizado ativo e, em seguida, retreine e Republique. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Atribuir várias chave LUIS ao mesmo aplicativo
Se seu aplicativo LUIS receber mais ocorrências de ponto de extremidade do que a cota da sua única chave, crie e atribua mais chaves ao aplicativo LUIS. Crie um gerenciador de tráfego ou um balanceador de carga para gerenciar as consultas de ponto de extremidade nas chaves de ponto de extremidade. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando seu aplicativo monolítico retorna uma intenção errada
Se seu aplicativo deve prever uma ampla variedade de declarações do usuário, considere implementar o [modelo de expedição](#dispatch-tool-and-model). Dividir um aplicativo monolítico permite que o LUIS concentre a detecção entre intenções de maneira bem-sucedida, em vez de ficar confuso entre intenções no aplicativo pai e nos aplicativos filho. 

Agende um [exame de declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md) periódico para o aprendizado ativo, como a cada duas semanas e, em seguida, treine e publique novamente. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando você precisa ter mais de 500 intenções
Suponha que você esteja desenvolvendo um assistente do Office com mais de 500 tentativas. Se 200 intenções relacionarem-se ao agendamento de reuniões, 200 forem sobre lembretes, 200 se tratarem de obter informações sobre colegas e 200 forem para envio de email, agrupe as intenções para que cada grupo esteja em um único aplicativo e crie um aplicativo de nível superior que contém cada intenção. Use o [modelo de expedição](#dispatch-tool-and-model) para criar o aplicativo de nível superior. Em seguida, altere o bot para usar a chamada em cascata, conforme mostrado no [tutorial do modelo de expedição](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando você precisar combinar vários aplicativos LUIS e QnA Maker
Se você tiver vários aplicativos LUIS e QnA Maker que precisam responder a um bot, use o [modelo de expedição](#dispatch-tool-and-model) para criar o aplicativo de nível superior.  Em seguida, altere o bot para usar a chamada em cascata, conforme mostrado no [tutorial do modelo de expedição](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Ferramenta e modelo de expedição
Use a ferramenta de linha de comando [Dispatch][dispatch-tool] , encontrada em [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) para combinar vários LUIS e/ou QnA Maker aplicativos em um aplicativo Luis pai. Essa abordagem permite que você tenha um domínio pai incluindo todos os sujeitos e diferentes domínios filhos de entidade em aplicativos separados. 

![Imagem conceitual da arquitetura de expedição](./media/luis-concept-enterprise/dispatch-architecture.png)

O domínio pai é indicado no LUIS com uma versão chamada `Dispatch` na lista de aplicativos. 

O bot de chat recebe o expressão e, em seguida, envia para o aplicativo LUIS pai para previsão. A principal intenção prevista do aplicativo pai determina qual aplicativo filho LUIS é chamado em seguida. O bot de chat envia o expressão para o aplicativo filho para uma previsão mais específica.

Entenda como essa hierarquia de chamadas é feita do Bot Builder v4 [Dispatcher-Application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limites de intenção no modelo de expedição
Um aplicativo de expedição tem 500 fontes de expedição, equivalentes a 500 intenções, como o máximo. 

## <a name="more-information"></a>Mais informações

* [SDK do bot Framework](https://github.com/Microsoft/botframework)
* [Tutorial do modelo de expedição](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [CLI de expedição](https://github.com/Microsoft/botbuilder-tools)
* Exemplo de bot de modelo de expedição – [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [testar um lote](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
