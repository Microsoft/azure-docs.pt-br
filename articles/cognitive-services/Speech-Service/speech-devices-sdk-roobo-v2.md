---
title: Speech Devices SDK Roobo Smart Audio dev kit v2 – Speech Service
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para introdução ao SDK de dispositivos de fala, kit de desenvolvimento inteligente de áudio Roobo v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80371590"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Dispositivo: kit de desenvolvimento inteligente de áudio Roobo v2

Este artigo fornece informações específicas do dispositivo para o Roobo de desenvolvimento inteligente de áudio do kit2.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores micro USB. O conector à esquerda serve para alimentar o kit de desenvolvimento e está realçado como Ligar/Desligar na imagem abaixo. O da direita serve para controlá-lo e está marcado como Depurar na imagem. 
    ![Conectar o kit de desenvolvimento](media/speech-devices-sdk/roobo-v2-connections.png)
1. Ligue o kit de desenvolvimento usando um cabo micro USB para conectar a porta de energia a um PC ou adaptador de energia. Um indicador de energia verde acenderá sob o quadro superior.
1. Para controlar o kit de desenvolvimento, conecte a porta de depuração a um computador usando um segundo cabo micro USB. É essencial usar um cabo de alta qualidade para garantir comunicações confiáveis.
1. Oriente seu kit de desenvolvimento circularmente-verticalmente, com microfones voltados para o teto, conforme mostrado acima


## <a name="development-information"></a>Informações de desenvolvimento

Para obter mais informações de desenvolvimento, consulte o [Guia de desenvolvimento do Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Registro/reprodução de áudio

As operações de áudio DDK2 podem ser executadas das seguintes maneiras:
* Use bibliotecas de código-fonte aberto do ALSA e seus aplicativos.
* Use a interface appmainprog para fazer o desenvolvimento de aplicativos. A estrutura de software relacionada a áudio DDK2 usa a estrutura ALSA padrão, você pode usar o libasound. Portanto, para desenvolver software diretamente. Portanto, você pode usar o arecord e o aplay da ALSA diretamente para gravar e reproduzir áudio.
