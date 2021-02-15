---
title: Mapa do índice de corpo do Azure Kinect
description: Entenda como consultar um mapa de índice de acompanhamento de corpo no Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, portabilidade, corpo, acompanhamento, índice, segmentação, mapa
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276404"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Mapa do índice de acompanhamento do corpo do Azure Kinect

O mapa do índice de corpo inclui o mapa de segmentação de instância para cada corpo na captura de câmera de profundidade. Cada pixel é mapeado para o pixel correspondente na imagem de profundidade ou de IR. O valor de cada pixel representa a qual corpo o pixel pertence. Pode ser em segundo plano (valor `K4ABT_BODY_INDEX_MAP_BACKGROUND` ) ou o índice de um detectado `k4abt_body_t` .

![Exemplo de mapa de índice de corpo](./media/concepts/body-index-map.png)

>[!NOTE]
> O índice do corpo é diferente da ID do corpo. Você pode consultar a ID de corpo de um determinado índice de corpo chamando API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92).


## <a name="using-body-index-map"></a>Usando o mapa do índice do corpo

O mapa do índice de corpo é armazenado como um `k4a_image_t` e tem a mesma resolução que a imagem de profundidade ou de ir. Cada pixel é um valor de 8 bits. Ele pode ser consultado de um `k4abt_frame_t` chamando `k4abt_frame_get_body_index_map` . O desenvolvedor é responsável por liberar a memória para o mapa do índice de corpo chamando `k4a_image_release()` .

## <a name="next-steps"></a>Próximas etapas

[Criar seu primeiro aplicativo de acompanhamento de corpo](build-first-body-app.md)
