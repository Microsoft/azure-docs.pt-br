---
title: Serviços de comunicação do Azure-problemas conhecidos
description: Saiba mais sobre os problemas conhecidos com os serviços de comunicação do Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628129"
---
# <a name="known-issues-azure-communication-services"></a>Problemas conhecidos: serviços de comunicação do Azure

Este artigo fornece informações sobre problemas conhecidos associados aos serviços de comunicação do Azure.

## <a name="video-streaming-quality-on-chromeandroid"></a>Qualidade de streaming de vídeo no Chrome/Android 

O desempenho de streaming de vídeo pode ser degradado no Android do Chrome.

### <a name="possible-causes"></a>Possíveis causas
A qualidade dos fluxos remotos depende da resolução do processador do lado do cliente que foi usado para iniciar esse fluxo. Ao assinar um fluxo remoto, um receptor determinará sua própria resolução com base nas dimensões do processador do lado do cliente do remetente.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Microfones do headset Bluetooth não são detectados

Você pode enfrentar problemas ao conectar seu headset Bluetooth a uma chamada de serviços de comunicação.

### <a name="possible-causes"></a>Possíveis causas
Não há uma opção para selecionar o microfone Bluetooth no iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Alternar repetidamente dispositivos de vídeo pode fazer com que o streaming de vídeo pare temporariamente

Alternar entre dispositivos de vídeo pode fazer com que o fluxo de vídeo seja pausado enquanto o fluxo é adquirido do dispositivo selecionado.

### <a name="possible-causes"></a>Possíveis causas
A transmissão e a alternância entre os dispositivos de mídia são computacionalmente intensivas. Alternar com frequência pode causar degradação do desempenho. Os desenvolvedores são incentivados a interromper um fluxo de dispositivo antes de iniciar outro.
