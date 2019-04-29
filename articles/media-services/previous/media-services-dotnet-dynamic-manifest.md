---
title: Criando filtros com o SDK do .NET dos Serviços de Mídia do Azure
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 05b899658b5c58e15b2f30ab759eb49319979fee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465551"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Criar filtros com o SDK do .NET dos Serviços de Mídia 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A partir da versão 2.17, os Serviços de Mídia do Microsoft Azure permitem definir filtros para seus ativos. Esses filtros são regras do lado do servidor que permitem aos clientes optar por realizar ações como: reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro) ou especificar apenas um subconjunto de representações de áudio e vídeo com o qual o dispositivo do cliente pode lidar (em vez de todas as representações que estão associadas ao ativo). A filtragem de ativos é obtida por meio de **Manifestos Dinâmicos**criados mediante solicitação do cliente para transmitir um vídeo com base em filtros especificados.

Para obter mais informações relacionadas a filtros e ao Manifesto Dinâmico, consulte [Visão geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

Este artigo mostra como usar o SDK .NET dos Serviços de Mídia para criar, atualizar e excluir filtros. 

Observe que, se atualizar um filtro, talvez sejam necessários até dois minutos para que o ponto de extremidade do streaming atualize as regras. Se o conteúdo foi servido usando esse filtro (e armazenado em cache nos proxies e caches CDN), atualizar esse filtro pode resultar em falhas do player. Sempre limpe o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente. 

## <a name="types-used-to-create-filters"></a>Tipos usados para criar filtros
Os tipos a seguir são usados durante a criação de filtros: 

* **IStreamingFilter**.  Esse tipo é baseado na seguinte API REST [filtro](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Esse tipo é baseado na seguinte API REST [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Esse tipo é baseado na seguinte API REST [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** e **IFilterTrackPropertyCondition**. Esses tipos são baseados nas seguintes APIs REST [FilterTrackSelect e FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Criar/atualizar/ler/excluir os filtros globais
O código a seguir mostra como usar o .NET para criar, atualizar, ler e excluir filtros de ativo.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Criar/atualizar/ler/excluir os filtros de ativo
O código a seguir mostra como usar o .NET para criar, atualizar, ler e excluir filtros de ativo.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Criar URLs de streaming que usam filtros
Para obter informações sobre como publicar e distribuir seus ativos, consulte [Visão geral do fornecimento de conteúdo a clientes](media-services-deliver-content-overview.md).

Os exemplos a seguir mostram como adicionar filtros às URLs de streaming.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Streaming Suave**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Visão geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md)

