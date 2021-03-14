---
title: Visão geral do empacotamento dinâmico dos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de Serviços de Mídia do Microsoft Azure empacotamento dinâmico.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d2394cba157a7049e3b63b09fda9e018f0a0b32f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013611"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/dynamic-packaging-overview.md)
> * [Versão 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Os Serviços de Mídia do Microsoft Azure podem ser usados para fornecer vários formatos de arquivos de mídia de origem, formatos de streaming de mídia e formatos de proteção de conteúdo para uma variedade de tecnologias de cliente (por exemplo, iOS, XBOX, Silverlight, Windows 8). Esses clientes entendem protocolos diferentes, por exemplo: o iOS requer um formato HTTP Live Streaming (HLS) V4, enquanto Silverlight e Xbox requerem Smooth Streaming. Se você tiver um conjunto de arquivos MP4 (mídia base ISO 14496-12) de taxa de bits adaptável (múltiplas taxas de bits), ou um conjunto de arquivos Smooth Streaming de taxa de bits adaptável que você deseja fornecer a clientes que entendam MPEG DASH, HLS ou Smooth Streaming, você deve tirar proveito do empacotamento dinâmico dos Serviços de Mídia.

Com o empacotamento dinâmico, é necessário criar um ativo que contenha um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Em seguida, com base no formato especificado na solicitação de fragmento ou manifesto, o servidor de Streaming OnDemand garantirá que você receba o fluxo no protocolo escolhido por você. Como resultado você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia vão criar e fornecer a resposta apropriada com base nas solicitações de um cliente.

O diagrama a seguir mostra a codificação tradicional e o fluxo de trabalho de empacotamento estático.

![Codificação estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama a seguir mostra o fluxo de trabalho de empacotamento dinâmico.

![Codificação dinâmica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Cenário comum

1. Carrega um arquivo de entrada (chamado de arquivo de mezanino). Por exemplo, H.264, MP4 ou WMV (para obter a lista de formatos com suporte, consulte [Formatos com suporte do Codificador de Mídia Padrão](media-services-media-encoder-standard-formats.md)).
2. Codifique o arquivo de mezanino para conjuntos de taxa de bits adaptável MP4 H.264.
3. Publique o ativo que contém a taxa de bits adaptável MP4 definida ao criar o localizador OnDemand.
4. Crie as URLs de streaming para acessar e transmitir seu conteúdo.

## <a name="preparing-assets-for-dynamic-streaming"></a>Preparação de ativos para streaming dinâmico

Para preparar seu ativo para streaming dinâmico, você tem as seguintes opções:

- [Carregue um arquivo mestre](media-services-dotnet-upload-files.md).
- [Use o Codificador de Mídia Padrão para produzir conjuntos de taxa de bits adaptáveis MP4 H.264](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Transmita seu conteúdo](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportados por embalagem dinâmica

O empacotamento dinâmico dá suporte a arquivos MP4, que contêm áudio codificado com [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 ou e-AC3), Dolby Atmos ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD lossless). O streaming do conteúdo Dolby Atmos tem suporte para padrões como o protocolo MPEG-DASH com o formato de streaming comum (CSF) ou o MP4 fragmentado do CMAF (formato de aplicativo de mídia comum) e via HTTP Live Streaming (HLS) com CMAF.

> [!NOTE]
> O Dynamic Packaging não suporta arquivos que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec herdado).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
