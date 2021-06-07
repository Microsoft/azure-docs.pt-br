---
title: Serviços de comunicação do Azure-problemas conhecidos
description: Saiba mais sobre os serviços de comunicação do Azure
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 7be40ac5f6cda7a81d68ca0b17f377891dd58480
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606038"
---
# <a name="known-issues-azure-communication-services-sdks"></a>Problemas conhecidos: SDKs dos serviços de comunicação do Azure
Este artigo fornece informações sobre limitações e problemas conhecidos relacionados aos SDKs dos serviços de comunicação do Azure.

> [!IMPORTANT]
> Há vários fatores que podem afetar a qualidade da sua experiência de chamada. Consulte a documentação de **[requisitos de rede](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** para saber mais sobre as práticas recomendadas de configuração e teste de rede dos serviços de comunicação.


## <a name="javascript-sdk"></a>SDK do JavaScript

Esta seção fornece informações sobre problemas conhecidos associados aos SDKs de chamada de vídeo e voz JavaScript dos serviços de comunicação do Azure.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>A atualização de uma página não remove imediatamente o usuário da chamada

Se um usuário estiver em uma chamada e decidir atualizar a página, o serviço de mídia dos serviços de comunicação não removerá esse usuário imediatamente da chamada. Ele aguardará que o usuário ingresse novamente. O usuário será removido da chamada depois que o serviço de mídia atingir o tempo limite.

É melhor criar experiências do usuário que não exijam que os usuários finais atualizem a página de seu aplicativo enquanto estiverem em uma chamada. Se um usuário atualizar a página, reutilize a mesma ID de usuário dos serviços de comunicação depois de retornar ao aplicativo.

Da perspectiva de outros participantes na chamada, o usuário permanecerá na chamada por período de tempo (1-2 minutos). Se o usuário se reassociar com a mesma ID de usuário dos serviços de comunicação, ele será representado como o mesmo objeto existente na `remoteParticipants` coleção.

Se o usuário estava enviando vídeo antes de atualizar, a `videoStreams` coleção manterá as informações de fluxo anteriores até que o serviço expire e a remova. Nesse cenário, o aplicativo pode decidir observar quaisquer novos fluxos adicionados à coleção e renderizar um com o mais alto `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Não é possível renderizar várias visualizações de vários dispositivos na Web
Essa é uma limitação conhecida. Para obter mais informações, consulte a [visão geral do SDK de chamada](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>A enumeração de dispositivos não é possível no Safari quando o aplicativo é executado no iOS ou no iPadOS

Os aplicativos não podem enumerar/selecionar dispositivos MIC/viva-voz (como Bluetooth) no Safari iOS/iPad. Essa é uma limitação conhecida do sistema operacional.

Se você estiver usando o Safari no macOS, seu aplicativo não poderá enumerar/selecionar alto-falantes por meio do Gerenciador de Dispositivos de serviços de comunicação. Nesse cenário, os dispositivos devem ser selecionados por meio do sistema operacional. Se você usar o Chrome no macOS, o aplicativo poderá enumerar/selecionar dispositivos por meio do Gerenciador de Dispositivos de serviços de comunicação.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>A conectividade de áudio é perdida ao receber mensagens SMS ou chamadas durante uma chamada VoIP contínua
Os navegadores móveis não mantêm a conectividade no estado de segundo plano. Isso pode levar a uma experiência de chamada degradada se a chamada VoIP foi interrompida por um evento que envia o aplicativo por push para o plano de fundo.

<br/>Biblioteca de cliente: chamando (JavaScript)
<br/>Navegadores: Safari, Chrome
<br/>Sistema operacional: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Alternar repetidamente dispositivos de vídeo pode fazer com que o streaming de vídeo pare temporariamente

Alternar entre dispositivos de vídeo pode fazer com que o fluxo de vídeo seja pausado enquanto o fluxo é adquirido do dispositivo selecionado.

#### <a name="possible-causes"></a>Possíveis causas
Alternar entre dispositivos frequentemente pode causar degradação do desempenho. Os desenvolvedores são incentivados a interromper um fluxo de dispositivo antes de iniciar outro.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>O microfone do headset Bluetooth não é detectado, portanto não é audível durante a chamada no Safari no iOS
Não há suporte para Headsets Bluetooth no Safari no iOS. Seu dispositivo Bluetooth não será listado nas opções de microfone disponíveis e outros participantes não poderão ouvi-lo se você tentar usar o Bluetooth no Safari.

#### <a name="possible-causes"></a>Possíveis causas
Essa é uma limitação conhecida do sistema operacional macOS/iOS/iPadOS. 

Com o Safari no **MacOS** e **Ios/iPadOS**, não é possível enumerar/selecionar dispositivos de alto-falantes por meio dos serviços de comunicação Gerenciador de dispositivos uma vez que a enumeração/seleção de alto-falantes não é suportada pelo Safari. Nesse cenário, a seleção do dispositivo deve ser atualizada por meio do sistema operacional.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>A rotação de um dispositivo pode criar qualidade de vídeo ruim
Os usuários podem experimentar a qualidade de vídeo degradada quando os dispositivos são girados.

<br/>Dispositivos afetados: Google pixel 5, Google pixel 3A, Apple iPad 8 e Apple iPad X
<br/>Biblioteca de cliente: chamando (JavaScript)
<br/>Navegadores: Safari, Chrome
<br/>Sistema operacional: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>A alternância de câmera faz o congelamento da tela 
Quando um usuário de serviços de comunicação ingressa em uma chamada usando o SDK de chamada de JavaScript e, em seguida, acessa o botão de alternância de câmera, a interface do usuário pode ficar sem resposta até que o aplicativo seja atualizado ou o navegador seja enviado por push para o plano de fundo por usuários.

<br/>Dispositivos afetados: Google pixel 4a
<br/>Biblioteca de cliente: chamando (JavaScript)
<br/>Navegadores: Chrome
<br/>Sistema operacional: iOS, Android


#### <a name="possible-causes"></a>Possíveis causas
Sob Investigação.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Se o sinal de vídeo foi interrompido enquanto a chamada está no estado "conectando", o vídeo não será enviado após a chamada ser iniciada 
Se os usuários decidirem ativar/desativar o vídeo rapidamente enquanto a chamada estiver no `Connecting` estado-isso pode levar a um problema com o fluxo adquirido para a chamada. Incentivamos os desenvolvedores a criarem seus aplicativos de uma maneira que não exija que o vídeo seja ativado/desativado enquanto a chamada estiver no `Connecting` estado. Esse problema pode causar um desempenho de vídeo degradado nos seguintes cenários:

 - Se o usuário começar com áudio e iniciar e parar o vídeo enquanto a chamada estiver em `Connecting` estado.
 - Se o usuário começar com áudio e iniciar e parar o vídeo enquanto a chamada estiver em `Lobby` estado.


#### <a name="possible-causes"></a>Possíveis causas
Sob Investigação.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Às vezes, leva muito tempo para renderizar vídeos de participantes remotos
Durante uma chamada de grupo em andamento, o _usuário A_ envia vídeo e, em seguida, o _usuário B_ une a chamada. Às vezes, o usuário B não vê vídeo A partir do usuário A ou o vídeo de um usuário começa a renderizar após um longo atraso. Esse problema pode ser causado por um ambiente de rede que requer configuração adicional. Consulte a documentação de [requisitos de rede](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) para obter diretrizes de configuração de rede.
