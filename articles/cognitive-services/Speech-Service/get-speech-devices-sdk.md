---
title: Obter SDK dos dispositivos de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala funciona com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. Neste artigo, você aprenderá a obter acesso ao SDK de dispositivos de fala e começar a desenvolver.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026362"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Fala dos Serviços Cognitivos

O SDK de dispositivos de fala é uma biblioteca predefinida projetada para funcionar com kits de desenvolvimento criados por finalidade e diversas configurações de matriz de microfone.

## <a name="choose-a-development-kit"></a>Escolha um kit de desenvolvimento

|Dispositivos|Especificação|Descrição|Cenários|
|--|--|--|--|
|Kit de desenvolvimento do [Urbetter](http://www.urbetter.com/products_56/278.html) ![ URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 matriz MIC, SOC do ARM, WIFI, Ethernet, HDMI, câmera USB. <br>Linux|Um SDK de dispositivos de fala no nível do setor que adapta a matriz de Mic da Microsoft e dá suporte a e/s estendida, como HDMI/Ethernet e mais periféricos USB <br> [Contate a Urbetter](http://www.urbetter.com/products_56/278.html)|Transcrição de conversa, educação, hospital, robôs, caixa OTT, agente de voz, unidade por meio|
|[Kit de desenvolvimento de áudio inteligente Roobo](http://ddk.roobo.com)<br>[Instalação](speech-devices-sdk-roobo-v1.md)  /  do [Início rápido](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) ![ Kit de desenvolvimento de áudio inteligente Roobo](media/speech-devices-sdk/device-roobo-v1.jpg)|7 matriz do MIC, SOC do ARM, WIFI, saída de áudio, e/s. <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|O primeiro SDK de dispositivos de fala para adaptar a matriz MIC da Microsoft e o SDK de processamento frontal, para desenvolver cenários de transcrição e de fala de alta qualidade|Transcrição de conversa, palestrante inteligente, agente de voz, portátil|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Instalação](../../kinect-dk/set-up-azure-kinect-dk.md)  /  do [Início rápido](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) ![ Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 a matriz MIC RGB e câmeras de profundidade. <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) / [Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Um kit de desenvolvedor com sensores de ia (inteligência artificial avançada) para a criação de modelos sofisticados de visão e fala de computadores. Ele combina uma melhor matriz de microfone espacial e uma câmera de profundidade com uma câmera de vídeo e um sensor de orientação, tudo em um dispositivo pequeno com vários modos, opções e SDKs para acomodar uma variedade de tipos de computação.|Transcrição de conversa, robótica, criação inteligente|
|Kit de desenvolvimento de áudio inteligente do Roobo 2<br>[Instalação](speech-devices-sdk-roobo-v2.md)<br>![Kit de desenvolvimento de áudio inteligente do Roobo 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 matriz MIC, SOC do ARM, WIFI, Bluetooth, e/s. <br>Linux|O SDK de 2ª geração de dispositivos de fala que fornece um sistema operacional alternativo e mais recursos em um design de referência econômico.|Transcrição de conversa, palestrante inteligente, agente de voz, portátil|


## <a name="download-the-speech-devices-sdk"></a>Fazer download do SDK de Dispositivos de Fala

Baixe o [SDK dos dispositivos de fala](./speech-devices-sdk.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao SDK de Dispositivos de Fala](./speech-devices-sdk-quickstart.md?pivots=platform-android)