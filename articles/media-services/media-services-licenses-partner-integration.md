---
title: "Como usar parceiros para fornecer licenças Widevine para os Serviços de Mídia do Azure | Microsoft Docs"
description: "Este artigo descreve como é possível usar os serviços de mídia do Azure (AMS) para oferecer um fluxo dinamicamente é criptografado pelo AMS com os DRMs do PlayReady e Widevine. A licença do PlayReady vem do servidor de licenças do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licenças do castLabs."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 6867e4f910970121df3858516c6bab3114c3c6f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Usando parceiros  para fornecer licenças Widevine para os Serviços de Mídia do Azure
## <a name="overview"></a>Visão geral
Os Serviços de Mídia do Microsoft Azure permitem o fornecimento de MPEG-DASH protegido por DRM Widevine, que é criptografado de acordo com a especificação de Criptografia Comum (CENC).

A partir da versão 3.5.2 do SDK do .NET dos Serviços de Mídia, os Serviços de Mídia permitem que você configure um modelo de licença do Widevine e obtenha licenças do Widevine. Você também pode usar os seguintes parceiros do AMS para ajudar no fornecimento de licenças do Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/).

## <a name="castlabs"></a>castLabs
Você pode usar [castLabs](http://castlabs.com/company/partners/azure/) para fornecer licenças Widevine. Para saber mais, consulte [Usando o castLabs para fornecer licenças DRM aos Serviços de Mídia do Azure](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
Você pode usar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para fornecer licenças Widevine. Para saber mais, consulte [Usando o Axinom para fornecer licenças DRM aos Serviços de Mídia do Azure](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Usando a PlayReady e/ou a criptografia comum dinâmica Widevine.](media-services-protect-with-drm.md)

[Blog do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

