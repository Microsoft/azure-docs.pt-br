---
title: Arquitetura de cliente e servidor
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre a arquitetura dos serviços de comunicação.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 8b63ce25d6839e8672027240099859dc7aad96c5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886066"
---
# <a name="client-and-server-architecture"></a>Arquitetura de cliente e servidor

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Cada aplicativo dos serviços de comunicação do Azure terá **aplicativos cliente** que usam **Serviços** para facilitar a conectividade de pessoa a pessoa. Esta página ilustra os elementos comuns de arquitetura em uma variedade de cenários.

## <a name="user-access-management"></a>Gerenciamento de acesso do usuário

As bibliotecas de cliente dos serviços de comunicação do Azure exigem o `user access tokens` acesso seguro aos recursos dos serviços de comunicação. `User access tokens` deve ser gerado e gerenciado por um serviço confiável devido à natureza confidencial do token e à cadeia de conexão necessária para gerá-los. A falha de gerenciar tokens de acesso corretamente pode resultar em cobranças adicionais devido ao uso indevido de recursos. É altamente recomendável fazer uso de um serviço confiável para o gerenciamento de usuários. O serviço confiável irá gerar os tokens e passá-los de volta para o cliente usando a criptografia adequada. Um fluxo de arquitetura de exemplo pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagrama mostrando a arquitetura de token de acesso do usuário.":::

Para obter informações adicionais, consulte [melhores práticas de gerenciamento de identidade](../../security/fundamentals/identity-management-best-practices.md)

## <a name="browser-communication"></a>Comunicação do navegador

As bibliotecas de cliente JavaScript de comunicações do Azure podem habilitar aplicativos Web com interação avançada de Rich Text, voz e vídeo. O aplicativo interage diretamente com os serviços de comunicação do Azure por meio da biblioteca de cliente para acessar o plano de dados e fornecer comunicação de texto, voz e vídeo em tempo real. Um fluxo de arquitetura de exemplo pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagrama mostrando a arquitetura do navegador para os serviços de comunicação.":::

## <a name="native-app-communication"></a>Comunicação de aplicativo nativo

Muitos cenários são mais bem atendidos com aplicativos nativos. Os serviços de comunicação do Azure dão suporte à comunicação de aplicativo para aplicativo e do navegador para o aplicativo.  Ao criar uma experiência de aplicativo nativa, o envio de notificações por push permitirá que os usuários recebam chamadas mesmo quando o aplicativo não estiver em execução. Os serviços de comunicação do Azure facilitam esse processo com notificações por push integradas ao Google firebase, Apple Push Notification Service e notificações por push do Windows. Um fluxo de arquitetura de exemplo pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagrama mostrando a arquitetura dos serviços de comunicação para comunicação de aplicativo nativo.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Voz e SMS sobre a rede de telefonia comutada pública (PSTN)

A comunicação por meio do sistema telefônico pode aumentar consideravelmente o alcance do seu aplicativo. Para dar suporte a cenários de voz e SMS PSTN, os serviços de comunicação do Azure ajudam a [adquirir números de telefone](../quickstarts/telephony-sms/get-phone-number.md) diretamente do portal do Azure ou usando bibliotecas de cliente e APIs REST. Depois que os números de telefone são adquiridos, eles podem ser usados para alcançar clientes usando a chamada PSTN e o SMS nos cenários de entrada e de saída. Um fluxo de arquitetura de exemplo pode ser encontrado abaixo:

> [!Note]
> Durante a visualização pública, o provisionamento de números de telefone dos EUA está disponível para clientes com endereços de cobrança localizados dentro dos EUA e do Canadá. 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagrama mostrando a arquitetura PSTN dos serviços de comunicação.":::

Para obter mais informações sobre as soluções PSTN e SMS, consulte [planejar sua solução PSTN e SMS](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>Pessoas se comunicando com bots e outros serviços

Os serviços de comunicação do Azure dão suporte à comunicação humana para o sistema por meio de canais de texto e voz, com serviços que acessam diretamente o plano de dados dos serviços de comunicação do Azure. Por exemplo, você pode fazer com que um bot responda a chamadas telefônicas recebidas ou participe de um bate-papo na Web. Os serviços de comunicação do Azure fornecem bibliotecas de cliente que habilitam esses cenários para chamada e chat. Um fluxo de arquitetura de exemplo pode ser encontrado abaixo:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagrama mostrando a arquitetura de bot dos serviços de comunicação.":::

## <a name="networking"></a>Rede

Talvez você queira trocar dados arbitrários entre os usuários, por exemplo, para sincronizar uma experiência mista compartilhada ou de jogos. O plano de dados em tempo real usado para comunicação de texto, voz e vídeo está disponível diretamente de duas maneiras:

- **Chamando a biblioteca de cliente** -os dispositivos em uma chamada têm acesso a APIs para enviar e receber dados pelo canal de chamada. Essa é a maneira mais fácil de adicionar comunicação de dados a uma interação existente.
- **Stun/Turn** – os serviços de comunicação do Azure tornam os stun em conformidade com os padrões e tornam os serviços disponíveis para você. Isso permite que você crie uma camada de transporte altamente personalizada sobre esses primitivos padronizados. Você pode criar seu próprio cliente compatível com padrões ou usar bibliotecas de software livre, como [WinRTC](https://github.com/microsoft/winrtc).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar tokens de acesso do usuário](../quickstarts/access-tokens.md)

Para obter mais informações, consulte os seguintes artigos:

- Saiba mais sobre a [autenticação](../concepts/authentication.md)
- Saiba mais sobre as [soluções PSTN e SMS](../concepts/telephony-sms/plan-solution.md)

- [Adicionar o chat ao seu aplicativo](../quickstarts/chat/get-started.md)
- [Adicionar chamada de voz ao aplicativo](../quickstarts/voice-video-calling/getting-started-with-calling.md)