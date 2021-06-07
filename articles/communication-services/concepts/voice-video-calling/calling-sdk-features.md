---
title: Visão geral do SDK de Chamada dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral do SDK de Chamada.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364865"
---
# <a name="calling-sdk-overview"></a>Visão geral do SDK de Chamada

O SDK de Chamada permite que os dispositivos do usuário final conduzam experiências de comunicação de voz e vídeo. Esta página fornece descrições detalhadas de recursos de Chamada, incluindo informações de suporte a plataformas e navegadores. Para começar imediatamente, confira [Guia de início rápido de Chamada](../../quickstarts/voice-video-calling/getting-started-with-calling.md) ou [Exemplo em destaque de Chamada](../../samples/calling-hero-sample.md). 

Depois de começar o desenvolvimento, confira a [página de problemas conhecidos](../known-issues.md) para encontrar os bugs nos quais estamos trabalhando.

Principais recursos do SDK de chamada:

- **Endereçamento** – os Serviços de Comunicação do Azure fornecem [identidades](../identity-model.md) genéricas que são usadas para lidar com pontos de extremidade de comunicação. Os clientes usam essas identidades para se autenticarem no serviço e se comunicarem. Essas identidades são usadas em APIs de Chamada, fornecendo aos clientes visibilidade de quem está conectado a uma chamada (a lista de participantes).
- **Criptografia** – o SDK de Chamada criptografa o tráfego e impede a adulteração da conexão. 
- **Gerenciamento de Dispositivos e Mídia** – o SDK de Chamada fornece recursos para associação a dispositivos de áudio e vídeo, codifica o conteúdo para transmissão eficiente no plano de dados de comunicação e renderiza o conteúdo para dispositivos de saída e exibições que você especificar. As APIs também são fornecidas para compartilhamento de tela e de aplicativo.
- **PSTN** – o SDK de Chamada pode receber e iniciar chamadas de voz com o tradicional sistema telefônico público comutado, [usando números de telefone adquiridos no portal do Azure](../../quickstarts/telephony-sms/get-phone-number.md) ou de modo programático.
- **Reuniões no Teams** – o SDK de Chamada pode [ingressar em reuniões do Teams](../../quickstarts/voice-video-calling/get-started-teams-interop.md) e interagir com o plano de dados de voz e vídeo do Teams. 
- **Notificações** – o SDK de Chamada fornece APIs que permitem que os clientes sejam notificados de uma chamada de entrada. Em situações em que seu aplicativo não está em execução em primeiro plano, os padrões estão disponíveis para [acionar notificações pop-up](../notifications.md) ("notificações do sistema") para informar os usuários finais de uma chamada de entrada. 

## <a name="detailed-capabilities"></a>Funcionalidades detalhadas 

A lista a seguir apresenta o conjunto de recursos que estão disponíveis nos SDKs de Chamada dos Serviços de Comunicação do Azure.

| Grupo de recursos | Funcionalidade                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Principais funcionalidades | Fazer uma chamada um-para-um entre dois usuários                                                                           | ✔️   | ✔️            | ✔️
|                   | Fazer uma chamada de grupo com mais de dois usuários (até 350 usuários)                                                       | ✔️   | ✔️            | ✔️
|                   | Promover uma chamada de um-para-um com dois usuários em uma chamada de grupo com mais de dois usuários                                 | ✔️   | ✔️            | ✔️
|                   | Ingressar em uma chamada de grupo depois que ela for iniciada                                                                              | ✔️   | ✔️            | ✔️
|                   | Convidar outro participante de VoIP para ingressar em uma chamada de grupo em andamento                                                       | ✔️   | ✔️            | ✔️
|  Controle de chamada média | Ativar/desativar seu vídeo                                                                                              | ✔️   | ✔️            | ✔️
|                   | Ativar/desativar mudo do microfone                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Alternar entre câmeras                                                                                              | ✔️   | ✔️            | ✔️
|                   | Ativar/desativar colocação em espera local                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Alto-falante ativo                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Escolher alto-falante para chamadas                                                                                            | ✔️   | ✔️            | ✔️
|                   | Escolher microfone para chamadas                                                                                         | ✔️   | ✔️            | ✔️
|                   | Mostrar o estado de um participante<br/>*Ocioso, Mídia inicial, Conectando, Conectado, Em espera, No lobby, Desconectado*         | ✔️   | ✔️            | ✔️
|                   | Mostrar o estado de uma chamada<br/>*Mídia inicial, Realizando, Conectando, Tocando, Conectada, Em espera, Desconectando, Desconectada* | ✔️   | ✔️            | ✔️
|                   | Mostrar se um participante está mudo                                                                                      | ✔️   | ✔️            | ✔️
|                   | Mostrar o motivo pelo qual um participante saiu de uma chamada                                                                       | ✔️   | ✔️            | ✔️
| Compartilhamento de tela    | Compartilhar a tela inteira de dentro do aplicativo                                                                 | ✔️   | ❌            | ❌
|                   | Compartilhar um aplicativo específico (na lista de aplicativos em execução)                                                | ✔️   | ❌            | ❌
|                   | Compartilhar uma guia do navegador da Web na lista de guias abertas                                                                  | ✔️   | ❌            | ❌
|                   | O participante pode exibir o compartilhamento de tela remoto                                                                            | ✔️   | ✔️            | ✔️
| Lista de participantes            | Listar participantes                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Remover um participante                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | Fazer uma chamada um-para-um com um participante PSTN                                                                     | ✔️   | ✔️            | ✔️
|                   | Fazer uma chamada de grupo com participantes PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Promover uma chamada um-para-um com um participante PSTN em uma chamada de grupo                                                 | ✔️   | ✔️            | ✔️
|                   | Discar de uma chamada de grupo como um participante PSTN                                                                    | ✔️   | ✔️            | ✔️
| Geral           | Teste o microfone, o alto-falante e a câmera com um serviço de teste de áudio (disponível chamando 8:echo123)                   | ✔️   | ✔️            | ✔️
| Gerenciamento de Dispositivo | Solicitar permissão para usar áudio e/ou vídeo                                                                       | ✔️   | ✔️            | ✔️
|                   | Obter lista de câmeras                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Definir a câmera                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Obter a câmera selecionada                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Obter lista de microfones                                                                                                 | ✔️   | ❌           |❌  
|                   | Definir o microfone                                                                                                      | ✔️   | ❌           | ❌  
|                   | Obter o microfone selecionado                                                                                             | ✔️   | ❌           | ❌  
|                   | Obter lista de alto-falantes                                                                                                   | ✔️   | ❌           | ❌  
|                   | Definir o alto-falante                                                                                                         | ✔️   | ❌           | ❌  
|                   | Obter o alto-falante selecionado                                                                                                | ✔️   | ❌           | ❌  
| Renderização de vídeo   | Renderizar um vídeo único em vários locais (câmera local ou fluxo remoto)                                                  | ✔️   | ✔️            | ✔️
|                   | Definir/atualizar o modo de dimensionamento                                                                                           | ✔️   | ✔️            | ✔️
|                   | Renderizar um fluxo de vídeo remoto                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Suporte para streaming do SDK de Chamada
O SDK de Chamada dos Serviços de Comunicação dá suporte às seguintes configurações de streaming:

| Limite          |Web | Android/iOS|
|-----------|----|------------|
|**Nº de fluxos de saída que podem ser enviados simultaneamente** |1 vídeo ou 1 compartilhamento de tela | 1 vídeo + 1 compartilhamento de tela|
|**Nº de fluxos de entrada que podem ser renderizados simultaneamente** |1 vídeo ou 1 compartilhamento de tela| 6 vídeos + 1 compartilhamento de tela |

## <a name="calling-sdk-timeouts"></a>Tempos limite do SDK de Chamada

Os seguintes tempos limite se aplicam aos SDKs de Chamada dos Serviços de Comunicação:

| Ação           | Tempo limite em segundos |
| -------------- | ---------- |
| Reconectar/remover participante | 120 |
| Adicionar ou remover nova modalidade de uma chamada (iniciar/parar o vídeo ou compartilhamento de tela) | 40 |
| Tempo limite da operação de Transferência de Chamada | 60 |
| Tempo limite de estabelecimento de chamada 1:1 | 85 |
| Tempo limite de estabelecimento de chamada de grupo | 85 |
| Tempo limite de estabelecimento de chamada PSTN | 115 |
| Tempo limite para promover chamada 1:1 a chamada de grupo | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Suporte ao SDK de Chamada JavaScript por sistema operacional e navegador

A tabela a seguir representa um conjunto de navegadores compatíveis que atualmente estão disponíveis. Damos suporte às três versões mais recentes do navegador, a menos que seja indicado de outro modo.

| Plataforma                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS***                         |  ✔️    | ✔️**   | ❌             |
| Windows***                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*As versões do Safari 13.1+ têm suporte, chamadas individuais no Safari não têm suporte.

**O Safari 14+ e o macOS 11+ são necessários para obter suporte ao vídeo de saída.

***O compartilhamento da tela de saída é compatível somente com plataformas de desktop (Windows, macOS e Linux), não importa a versão do navegador. Além disso, ele não é compatível com plataformas móveis (Android, iOS, iPad e tablets).

****Um aplicativo iOS não pode enumerar nem selecionar dispositivos de microfone e alto-falante no Safari (por exemplo, Bluetooth). Essa é uma limitação do sistema operacional e um dispositivo está sempre ativado.


## <a name="calling-client---browser-security-model"></a>Cliente chamador – modelo de segurança do navegador

### <a name="user-webrtc-over-https"></a>WebRTC do usuário por meio do HTTPS

APIs WebRTC como `getUserMedia` exigem que o aplicativo que chama essas APIs seja atendido por meio do HTTPS.

Para o desenvolvimento local, você pode usar `http://localhost`.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Inserir o SDK Chamador dos Serviços de Comunicação em um iframe

Uma nova [política de permissões (também chamada de política de recurso)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) está sendo adotada por vários navegadores. Essa política afeta a chamada de cenários controlando como os aplicativos podem acessar a câmera e o microfone de um dispositivo por meio de um elemento iframe entre origens.

Se você quiser usar um iframe para hospedar parte do aplicativo de um domínio diferente, será necessário adicionar o atributo `allow` com o valor correto ao seu iframe.

Por exemplo, esse iframe permite o acesso à câmera e ao microfone:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a chamadas](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, consulte os seguintes artigos:
- Familiarize-se com [fluxos de chamada](../call-flows.md) gerais
- Saiba mais sobre [tipos de chamada](../voice-video-calling/about-call-types.md)
- [Planeje sua solução PSTN](../telephony-sms/plan-solution.md)
