---
title: Gravação de vídeo contínua-Azure
description: A CVR (gravação contínua de vídeo) refere-se ao processo de gravar continuamente o vídeo de uma fonte de vídeo. Este tópico discute o que é o CVR.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453743"
---
# <a name="continuous-video-recording"></a>Gravação de vídeo contínua  

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

* [Conceito de grafo de mídia](media-graph-concept.md)
* [Conceito de gravação de vídeo](video-recording-concept.md)

## <a name="overview"></a>Visão geral

A CVR (gravação contínua de vídeo) refere-se ao processo de gravar continuamente o vídeo de uma fonte de vídeo. A análise de vídeo ao vivo em IoT Edge dá suporte à gravação contínua de vídeo, em uma base 24x7, de uma câmera CCTV por meio de um [grafo de mídia](media-graph-concept.md) que consiste em um nó de origem RTSP e um nó de coletor de ativos. O diagrama a seguir mostra uma representação gráfica desse grafo de mídia. A representação JSON da [topologia do grafo](media-graph-concept.md#media-graph-topologies-and-instances) desse grafo de mídia pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Gravação de vídeo contínua":::

O grafo de mídia descrito acima pode ser executado em um dispositivo de borda, com o vídeo de registro do coletor de ativos para um [ativo](terminology.md#asset)dos serviços de mídia do Azure. O vídeo será gravado enquanto o grafo de mídia permanecer no estado ativado. Como o vídeo está sendo gravado como um ativo, ele pode ser reproduzido usando os recursos de streaming existentes dos serviços de mídia. Consulte [reprodução de conteúdo registrado](video-playback-concept.md) para obter mais detalhes.

## <a name="resilient-recording"></a>Gravação resiliente

A análise de vídeo ao vivo no IoT Edge dá suporte à operação em condições de rede menos do que a perfeita, em que o dispositivo de borda pode ocasionalmente perder conectividade com a nuvem ou experimentar uma queda na largura de banda disponível. Para considerar isso, o vídeo da fonte é gravado localmente em um cache e sincronizado automaticamente com o ativo periodicamente. Se você examinar o [JSON da topologia do grafo](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json), verá que ele tem as seguintes propriedades definidas:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

As duas últimas propriedades são relevantes para a gravação resiliente (também são propriedades obrigatórias para um nó de coletor de ativos). A propriedade localMediaCachePath informa o coletor de ativos para usar esse caminho de pasta para armazenar em cache os dados de mídia antes de carregar para o ativo. Você pode ver [este](../../iot-edge/how-to-access-host-storage-from-module.md) artigo para entender como o módulo de borda pode usar o armazenamento local do dispositivo. A propriedade localMediaCacheMaximumSizeMiB define a quantidade de espaço em disco que o coletor de ativos pode usar como um cache (1 MiB = 1024 * 1024 bytes). 

Se o seu módulo de borda perder a conectividade por um tempo muito longo e o conteúdo armazenado na pasta de cache atingir o valor localMediaCacheMaximumSizeMiB, o coletor de ativos começará a descartar os dados do cache, a partir dos dados mais antigos. Por exemplo, se o dispositivo perdeu a conectividade em 10h e o cache atinge o limite máximo de 18:00, o coletor de ativos começa a excluir os dados registrados em 10h. 

Quando a conectividade de rede for restaurada, o coletor de ativos começará a carregar do cache, começando a partir dos dados mais antigos. No exemplo acima, suponha que 5 minutos de vídeo precisem ser descartados do cache no momento em que a conectividade foi restaurada (digamos, em 6:02PM), então o coletor de ativos começará a carregar da marca 10:05AM.

Se posteriormente você examinar o ativo usando [essas](playback-recordings-how-to.md) APIs, verá que há uma lacuna no ativo de aproximadamente 10h a 10:05AM.

## <a name="segmented-recording"></a>Gravação segmentada  

Conforme discutido acima, o nó do coletor de ativos gravará vídeo em um cache local e carregará periodicamente o vídeo na nuvem. A propriedade segmentLength (mostrada na seção acima) o ajudará a controlar o custo de transações de gravação associado à gravação de dados em sua conta de armazenamento em que o ativo está sendo registrado. Por exemplo, se você aumentar o período de carregamento de 30 segundos para 5 minutos, o número de transações de armazenamento diminuirá por um fator de 10 (5 * 60/30).

A propriedade segmentLength garante que o módulo de borda carregará vídeo no máximo uma vez por segmentLength segundos. Essa propriedade tem um valor mínimo de 30 segundos (também o padrão) e pode ser aumentada em incrementos de 30 segundos para um máximo de 5 minutos.

> [!NOTE]
> Consulte o artigo [gravações de reprodução](playback-recordings-how-to.md) para o efeito que o segmentLength tem na reprodução.

## <a name="see-also"></a>Veja também

* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Reprodução de conteúdo gravado](video-playback-concept.md)

## <a name="next-steps"></a>Próximas etapas

[Tutorial: gravação de vídeo contínua](continuous-video-recording-tutorial.md)
