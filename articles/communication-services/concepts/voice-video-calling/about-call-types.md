---
title: Conceitos de voz e vídeo nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os tipos de chamada dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8a25f69019e194650bb6aa2f5b8ae19dd37fbc48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729160"
---
# <a name="voice-and-video-concepts"></a>Conceitos de voz e vídeo

Você pode usar os Serviços de Comunicação do Azure para fazer e receber chamadas de voz e vídeo individuais ou em grupo. Suas chamadas podem ser feitas a outros dispositivos conectados à Internet e a telefones antigos. Você pode usar os SDKs de JavaScript, Android ou iOS dos Serviços de Comunicação para criar aplicativos que permitem que os usuários falem entre si em conversas privadas ou em discussões em grupo. Os Serviços de Comunicação do Azure dão suporte a chamadas de e para serviços ou bots.

## <a name="call-types-in-azure-communication-services"></a>Tipos de chamadas nos Serviços de Comunicação do Azure

Você pode fazer vários tipos de chamadas nos Serviços de Comunicação do Azure. O tipo de chamadas que você faz determina seu esquema de sinalização, os fluxos de tráfego de mídia e o modelo de preços.

### <a name="voice-over-ip-voip"></a>VoIP (voz sobre IP)

Quando um usuário de seu aplicativo chama outro usuário de seu aplicativo por meio de uma conexão de Internet ou de dados, a chamada é feita por VoIP (voz sobre IP). Nesse caso, a sinalização e a mídia fluem pela Internet.

### <a name="public-switched-telephone-network-pstn"></a>PSTN (rede telefônica pública comutada)

Sempre que os usuários interagem com um número de telefone tradicional, as chamadas são facilitadas por chamadas de voz PSTN (rede telefônica pública comutada). Para fazer e receber chamadas PSTN, você precisa adicionar recursos de telefonia ao recurso de Serviços de Comunicação do Azure. Nesse caso, a sinalização e a mídia usam uma combinação de tecnologias baseadas em IP e PSTN para conectar seus usuários.

### <a name="one-to-one-call"></a>Chamada individual

Uma chamada individual nos Serviços de Comunicação do Azure ocorre quando um de seus usuários se conecta a outro usuário usando um de nossos SDKs. A chamada pode ser VoIP ou PSTN.

### <a name="group-call"></a>Chamada em grupo

Uma chamada em grupo nos Serviços de Comunicação do Azure ocorre quando três ou mais participantes se conectam entre si. Qualquer combinação de VoIP e usuários conectados a PSTN pode estar presente em uma chamada de grupo. Uma chamada individual pode ser convertida em uma chamada em grupo adicionando participantes à chamada. Um desses participantes pode ser um bot.

### <a name="supported-video-standards"></a>Padrões de vídeo com suporte
Damos suporte ao H.264 (MPEG-4).

### <a name="video-quality"></a>A qualidade de vídeo 
Damos suporte a Full HD 1080p nos SDKs nativos (iOS, Android). Para o SDK Web (JS), damos suporte a HD 720p Standard. A qualidade depende da largura de banda disponível.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a chamadas](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, consulte os seguintes artigos:
- Familiarize-se com [fluxos de chamada](../call-flows.md) gerais
- [Tipos de número de telefone](../telephony-sms/plan-solution.md)
- Saiba mais sobre as [funcionalidades do SDK de Chamada](../voice-video-calling/calling-sdk-features.md)
