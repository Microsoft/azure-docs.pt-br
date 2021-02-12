---
title: Visão geral da biblioteca de clientes de chamada dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral da biblioteca de clientes de chamada.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f949747649e351947ba65666fdb57c0beac3aa23
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492103"
---
# <a name="calling-client-library-overview"></a>Visão geral da biblioteca de clientes de chamada

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Há duas famílias separadas de bibliotecas de clientes de chamada, para *clientes* e *serviços.* No momento, as bibliotecas de clientes disponíveis se destinam a experiências do usuário final: sites e aplicativos nativos.

As bibliotecas de cliente para serviços ainda não estão disponíveis e fornecem acesso aos planos de dados brutos de voz e vídeo, adequados para a integração com bots e outros serviços.

## <a name="calling-client-library-capabilities"></a>Funcionalidades da biblioteca de clientes de chamada

A lista a seguir apresenta o conjunto de recursos que estão disponíveis atualmente nas bibliotecas de clientes de chamada dos Serviços de Comunicação do Azure.

| Grupo de recursos | Funcionalidade                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Principais funcionalidades | Fazer uma chamada um-para-um entre dois usuários                                                                           | ✔️   | ✔️            | ✔️  
|                   | Fazer uma chamada de grupo com mais de dois usuários (até 350 usuários)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Promover uma chamada de um-para-um com dois usuários em uma chamada de grupo com mais de dois usuários                                 | ✔️   | ✔️            | ✔️ 
|                   | Ingressar em uma chamada de grupo depois que ela for iniciada                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Convidar outro participante de VoIP para ingressar em uma chamada de grupo em andamento                                                       | ✔️   | ✔️            | ✔️
|                   | Ativar/desativar seu vídeo                                                         | ✔️   | ✔️            | ✔️ 
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
| Geral           | Teste o microfone, o alto-falante e a câmera com um serviço de teste de áudio (disponível chamando 8:echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>Suporte para bibliotecas de clientes de Chamada ao JavaScript por SO e navegador

A tabela a seguir representa o conjunto de navegadores e versões com suporte disponíveis no momento.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | Sistema operacional do iPad|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **Biblioteca de clientes de chamada** | Chrome*, novo Edge | Chrome *, Safari** | Chrome*  | Chrome* | Chrome* | Safari** | Safari** |


*Observe há suporte para a versão mais recente do Chrome, além das duas versões anteriores.<br/>

**Observe que há suporte para as versões posteriores à 13.1 do Safari. Ainda não há suporte para vídeo de saída para o Safari no macOS, mas há suporte no iOS. Só há suporte para o compartilhamento de tela de saída no iOS desktop. Atualmente, não estão disponíveis chamadas individuais e em grupo no Safari.

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

## <a name="calling-client-library-streaming-support"></a>Como chamar o suporte de streaming da biblioteca de clientes
A biblioteca de clientes de chamada dos Serviços de Comunicação dá suporte às seguintes configurações de streaming:

|           |Web | Android/iOS|
|-----------|----|------------|
|**Nº de fluxos de saída que podem ser enviados simultaneamente** |1 vídeo + 1 compartilhamento de tela | 1 vídeo |
|**Nº de fluxos de entrada que podem ser renderizados simultaneamente** |1 vídeo + 1 compartilhamento de tela| 6 vídeos + 1 compartilhamento de tela |


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a chamadas](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Para obter mais informações, consulte os seguintes artigos:
- Familiarize-se com [fluxos de chamada](../call-flows.md) gerais
- Saiba mais sobre [tipos de chamada](../voice-video-calling/about-call-types.md)
- [Planeje sua solução PSTN](../telephony-sms/plan-solution.md)
