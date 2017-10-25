---
title: "Predefinições de tarefa para MES (Media Encoder Standard) | Microsoft Docs"
description: "Este tópico apresenta uma visão geral das predefinições de exemplo definidas pelo serviço para MES (Media Encoder Standard)."
author: Juliako
manager: cfow
editor: johndeu
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: juliako
ms.openlocfilehash: 5753b1dffe5a1a4ee069b83f58e9c2dac433b89d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Predefinições de exemplo para MES (Media Encoder Standard)

O **Media Encoder Standard** define um conjunto de predefinições de codificação de sistema predefinido que pode ser usado ao criar trabalhos de codificação. Será recomendável usar a predefinição “Transmissão Adaptável” se você desejar codificar um vídeo para transmissão com os Serviços de Mídia. Ao especificar essa predefinição, o Media Encoder Standard [gerará uma escada de taxa de bits automaticamente](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Criar predefinições personalizadas de exemplos
Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Se for necessário personalizar uma predefinição de codificação, comece com uma das predefinições de sistema oferecidas nesta seção como um modelo para a configuração personalizada. Para obter explicações sobre o significado de cada elemento nessas predefinições, e os valores válidos para cada elemento, veja o tópico [Esquema do Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  Ao usar uma predefinição para codificações 4k, você deverá adquirir o tipo de unidade reservada `S3`. Para saber mais, confira [Como dimensionar a codificação](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Configuração padrão de rotação de vídeo em predefinições:
Ao trabalhar com o Media Encoder Standard, a rotação de vídeo é habilitada por padrão. Se o vídeo foi gravado em um dispositivo móvel no modo Retrato, essas predefinições giram eles para o modo de Paisagem antes da codificação.
 
## <a name="available-presets"></a>Predefinições disponíveis: 

 [H264 Taxas de Bits Múltiplas 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 kbps, e áudio AAC 5.1.  
  
 [H264 Taxas de Bits Múltiplas 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 6000 kbps a 400 kbps, e áudio AAC estéreo.  
  
 [H264 Taxas de Bits Múltiplas 16x9 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 8500 kbps a 200 kbps, e áudio AAC estéreo.  
  
 [H264 Taxas de Bits Múltiplas 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1900 kbps a 400 kbps, e áudio AAC 5.1.  
  
 [H264 Taxas de Bits Múltiplas 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1900 kbps a 400 kbps, e áudio AAC estéreo.  
  
 [H264 Taxas de Bits Múltiplas 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produz um conjunto de 12 arquivos MP4 alinhados a GOP, variando de 20000 kbps a 1000 kbps, e áudio AAC 5.1.  
  
 [H264 Taxas de Bits Múltiplas 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produz um conjunto de 12 arquivos MP4 alinhados a GOP, variando de 20000 kbps a 1000 kbps, e áudio AAC estéreo.  
  
 [H264 Taxas de Bits Múltiplas 4x3 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produz um conjunto de 8 arquivos MP4 alinhados a GOP, variando de 8500 kbps a 200 kbps, e áudio AAC estéreo.  
  
 [H264 Taxas de Bits Múltiplas 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1600 kbps a 400 kbps, e áudio AAC 5.1.  
  
 [H264 Taxas de Bits Múltiplas 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produz um conjunto de 5 arquivos MP4 alinhados a GOP, variando de 1600 kbps a 400 kbps, e áudio AAC estéreo.  
  
 [H264 Taxas de Bits Múltiplas 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produz um conjunto de 6 arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 kbps, e áudio AAC 5.1.  
  
 [H264 Taxas de Bits Múltiplas 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produz um conjunto de 6 arquivos MP4 alinhados a GOP, variando de 3400 kbps a 400 kbps, e áudio AAC estéreo.  
  
 [H264 Taxa de Bits Única 1080p Áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produz um único arquivo MP4 com uma taxa de bits de 6750 kbps e áudio AAC 5.1.  
  
 [H264 Taxa de Bits Única 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produz um único arquivo MP4 com uma taxa de bits de 6750 kbps e áudio AAC estéreo.  
  
 [H264 Taxa de Bits Única 4K Áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produz um único arquivo MP4 com uma taxa de bits de 18000 kbps e áudio AAC 5.1.  
  
 [H264 Taxa de Bits Única 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) produz um único arquivo MP4 com uma taxa de bits de 18000 kbps e áudio AAC estéreo.  
  
 [H264 Taxa de Bits Única 4x3 SD Áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produz um único arquivo MP4 com uma taxa de bits de 1800 kbps e áudio AAC 5.1.  
  
 [H264 Taxa de Bits Única 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produz um único arquivo MP4 com uma taxa de bits de 1800 kbps e áudio AAC estéreo.  
  
 [H264 Taxa de Bits Única 16x9 SD Áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produz um único arquivo MP4 com uma taxa de bits de 2200 kbps e áudio AAC 5.1.  
  
 [H264 Taxa de Bits Única 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produz um único arquivo MP4 com uma taxa de bits de 2200 kbps e áudio AAC estéreo.  
  
 [H264 Taxa de Bits Única 720p Áudio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produz um único arquivo MP4 com uma taxa de bits de 4500 kbps e áudio AAC 5.1.  
  
 [H264 Taxa de Bits Única 720p para Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) produz um único arquivo MP4 com uma taxa de bits de 2000 kbps e AAC estéreo.  
  
 [H264 Taxa de Bits Única 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produz um único arquivo MP4 com uma taxa de bits de 4500 kbps e áudio AAC estéreo.  
  
 [H264 Taxa de Bits Única SD de Alta Qualidade para Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produz um único arquivo MP4 com uma taxa de bits de 500 kbps e áudio AAC estéreo.  
  
 [H264 Taxa de Bits Única SD de Baixa Qualidade para Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produz um único arquivo MP4 com uma taxa de bits de 56 kbps e áudio AAC estéreo.  
  
 Para saber mais sobre os codificadores dos Serviços de Mídia, veja [Codificação sob demanda com os Serviços de Mídia do Azure](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/).
