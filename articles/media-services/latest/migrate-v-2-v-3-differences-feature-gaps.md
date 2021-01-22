---
title: Lacunas de recursos entre os serviços de mídia do Azure V2 e v3 | Microsoft Docs
description: Este artigo descreve as lacunas de recursos entre os serviços de mídia do Azure v2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 0f15c2bcd921c431dba1d1cce0454a6b2e752141
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690238"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Lacunas de recursos entre os serviços de mídia do Azure V2 e v3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-2.svg)

Esta parte das diretrizes de migração fornece informações detalhadas sobre as diferenças entre as APIs V2 e v3.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Lacunas de recursos entre as APIs V2 e v3

A API v3 tem as seguintes lacunas de recursos com a API v2. Alguns dos recursos avançados do Media Encoder Standard nas APIs v2 não estão disponíveis atualmente no V3:

- Inserção de uma faixa de áudio silenciosa quando a entrada não tem áudio, pois isso não é mais necessário com o Player de Mídia do Azure.

- Inserindo uma faixa de vídeo quando a entrada não tem vídeo.

- Eventos ao vivo com transcodificação atualmente não dão suporte à inserção de Slate MID-Stream e inserção de marcador de anúncio via chamada à API.

- O codificador do Azure Media Premium não terá mais suporte na v2. Se você estiver usando-o para a codificação HEVC de 8 bits, use o novo suporte HEVC no codificador Standard. 
    - Adicionamos suporte para o mapeamento de canal de áudio para o codificador Standard.  Consulte [áudio na documentação do Swagger de codificação dos serviços de mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Se você estivesse usando recursos avançados ou formatos de saída do produto licenciado de terceiros, como MXF ou ProRes, use a solução de parceiro do Azure do Telestream, que será transacional no momento da desativação da v2. Como alternativa, você pode usar as comunicações imagine ou [Bitmovin](http://bitmovin.com).

- Não há mais suporte para a propriedade "conjunto de disponibilidade" no ponto de extremidade de streaming na v2. Consulte o projeto de exemplo e as diretrizes para a entrega de [VOD de alta disponibilidade](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding) na API v3.

- Nos serviços de mídia v3, FairPlay IV não pode ser especificado. Embora não afete os clientes que usam os serviços de mídia para o empacotamento e a entrega de licença, pode ser um problema ao usar um sistema DRM de terceiros para fornecer as licenças FairPlay (modo híbrido).

- A criptografia de armazenamento do lado do cliente para proteção de ativos em repouso foi removida na API v3 e substituída pela criptografia do serviço de armazenamento para dados em repouso. As APIs v3 continuam a trabalhar com ativos criptografados de armazenamento existentes, mas não permitem a criação de novos.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
