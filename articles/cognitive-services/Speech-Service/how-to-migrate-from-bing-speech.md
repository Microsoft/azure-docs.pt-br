---
title: Migrar de fala do Bing para serviços de fala do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como migrar de uma assinatura existente de fala do Bing para os serviços de fala do Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653709"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar da Fala do Bing para o Serviço de Fala

Use este artigo para migrar seus aplicativos da API de Fala do Bing para o Serviço de Fala.

Este artigo descreve as diferenças entre as APIs de fala do Bing e os serviços de fala e sugere estratégias para migrar seus aplicativos. Sua chave de assinatura de API de fala do Bing não funcionará com o serviço de fala; Você precisará de uma nova assinatura de serviços de fala.

Uma única chave de assinatura de serviços de fala concede acesso aos recursos a seguir. Cada um deles é medido separadamente e, portanto, você será cobrado somente pelos recursos que usar.

* [Conversão de fala em texto](speech-to-text.md)
* [Conversão de fala em texto personalizada](https://cris.ai)
* [Conversão de texto em fala](text-to-speech.md)
* [Conversão de texto em vozes de fala personalizada](how-to-customize-voice-font.md)
* [Tradução de fala](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [SDK de Fala](speech-sdk.md) é um substituto funcional para as bibliotecas de cliente de Fala do Bing, mas usa uma API diferente.

## <a name="comparison-of-features"></a>Comparação de recursos

Os serviços de fala são muito semelhantes em fala do Bing, com as seguintes diferenças.

Recurso | Fala do Bing | Serviços de Fala | Detalhes
-|-|-|-
SDK do C++ | :heavy_minus_sign: | :heavy_check_mark: | Serviços de fala compatível com Windows e Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Serviços de fala dá suporte a dispositivos Android e fala.
SDK do C# | :heavy_check_mark: | :heavy_check_mark: | Serviços de fala dá suporte ao Windows 10, a plataforma Universal do Windows (UWP) e o .NET Standard 2.0.
Reconhecimento de fala contínua | 10 minutos | Ilimitada (com o SDK) | Suportam a até 10 minutos por chamada de fala do Bing e protocolos WebSocket de serviços de fala. No entanto, o SDK de Fala se reconecta automaticamente no tempo limite ou se desconecta.
Resultados intermediários ou parciais | :heavy_check_mark: | :heavy_check_mark: | Com o protocolo WebSockets ou SDK.
Modelos de fala personalizados | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Fala Personalizada.
Fontes de voz personalizadas | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Voz Personalizada.
Vozes de 24 KHz | :heavy_minus_sign: | :heavy_check_mark:
Reconhecimento de intenção de fala | Requer uma chamada separada à API LUIS | Integrada (com o SDK) |  É possível usar uma chave do LUIS com o Serviço de Fala.
Reconhecimento simples de intenção | :heavy_minus_sign: | :heavy_check_mark:
Transcrição em lote de arquivos de áudio longos | :heavy_minus_sign: | :heavy_check_mark:
Modo de reconhecimento | Manual por meio do URI do ponto de extremidade | Automático | O modo de reconhecimento não está disponível no Serviço de Fala.
Localidade do ponto de extremidade | Global | Regional | Os pontos de extremidade regionais melhoram a latência.
APIs REST | :heavy_check_mark: | :heavy_check_mark: | As APIs de REST de serviços de fala são compatíveis com a conversão de fala do Bing (ponto de extremidade diferente). As APIs REST são compatíveis com as funcionalidades de conversão de texto em fala e uma funcionalidade limitada de conversão de fala em texto.
Protocolos WebSockets | :heavy_check_mark: | :heavy_check_mark: | A API de WebSockets de serviços de fala é compatível com a conversão de fala do Bing (ponto de extremidade diferente). Se possível, migre para o SDK de Fala para simplificar seu código.
Chamadas à API de serviço a serviço | :heavy_check_mark: | :heavy_minus_sign: | Fornecidas na Fala do Bing por meio da Biblioteca de Serviço do C#.
SDK de código-fonte aberto | :heavy_check_mark: | :heavy_minus_sign: |

Os serviços de fala usam um modelo de preços com base no tempo (em vez de um modelo baseado em transação). Ver [preços dos serviços de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos que usam uma API de fala do Bing no desenvolvimento ou produção, você deve atualizá-los para usar os serviços de fala assim que possível. Consulte a [documentação de serviços de fala](index.yml) para SDKs disponíveis, exemplos de código e tutoriais.

Os serviços de fala [APIs REST](rest-apis.md) são compatíveis com as APIs de fala do Bing. Se você estiver usando as APIs de REST de fala do Bing, você precisa apenas alterar o ponto de extremidade REST e, em seguida, alterne para uma chave de assinatura de serviços de fala.

Os protocolos WebSocket de serviços de fala também são compatíveis com aqueles usados pelo fala do Bing. É recomendável que você use o SDK do Speech em vez de WebSockets para o novo desenvolvimento. É uma boa ideia migrar o código existente para o SDK também. No entanto, da mesma forma que com as APIs REST, o código existente que usa a Fala do Bing por meio de WebSockets requer apenas uma alteração no ponto de extremidade e uma chave atualizada.

Se você estiver usando uma biblioteca de clientes de Fala do Bing para uma linguagem de programação específica, migrar para o [SDK de Fala](speech-sdk.md) exigirá alterações no seu aplicativo porque a API é diferente. O SDK de Fala pode tornar seu código mais simples e dar acesso a novos recursos.

Atualmente, o SDK de Fala é compatível com C# (Windows 10, UWP, .NET Standard), Java (dispositivos Android e personalizados), Objective C (iOS), C++ (Windows e Linux) e JavaScript. As APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento multiplataforma.

Os serviços de fala não oferecem um ponto de extremidade global. Determine se o aplicativo funcionará de maneira eficiente usando um ponto de extremidade regional único para todo o tráfego. Caso contrário, use a geolocalização para determinar o ponto de extremidade mais eficiente. Você precisa de uma assinatura separada do serviços de fala em cada região em que você usar.

Se o aplicativo usar conexões de longa duração e não for possível usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos ao fazer a reconexão nos momentos apropriados.

Como começar a usar o SDK de Fala:

1. Faça o download do [SDK de Fala](speech-sdk.md).
1. Trabalho por meio dos serviços de fala [guias de início rápido](quickstart-csharp-dotnet-windows.md) e [tutoriais](how-to-recognize-intents-from-speech-csharp.md). Examine também os [exemplos de código](samples.md) para ter experiência com as novas APIs.
1. Atualize seu aplicativo para usar os serviços de fala.

## <a name="support"></a>Suporte

Os clientes de Fala do Bing devem entrar em contato com o atendimento ao cliente abrindo um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Você também pode entrar em contato conosco caso sua necessidade de suporte precise de um [plano de suporte técnico](https://azure.microsoft.com/support/plans/).

Para obter suporte de API, SDK e serviço de fala, visite os serviços de fala [página de suporte](support.md).

## <a name="next-steps"></a>Próximos passos

* [Experimente gratuitamente o serviços de fala](get-started.md)
* [Início Rápido: reconhecer a fala em um aplicativo UWP usando o SDK de Fala](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Consulte também
* [Notas de versão de serviços de fala](releasenotes.md)
* [O que é o Serviço de Fala](overview.md)
* [Documentação do SDK de fala e serviços de fala](speech-sdk.md#get-the-sdk)
