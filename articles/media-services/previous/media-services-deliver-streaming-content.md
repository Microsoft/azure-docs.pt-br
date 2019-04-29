---
title: Publicar o conteúdo dos Serviços de Mídia do Azure usando o .NET | Microsoft Docs
description: Saiba como criar um localizador que é usado para construir um URL de transmissão. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 49e9fbc3f8d10dd1e272f5f9bc5a542328a3ddb4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224218"
---
# <a name="publish-media-services-content-using-net"></a>Publicar o conteúdo dos Serviços de Mídia usando o .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Visão geral
Você pode transmitir um conjunto de MP4 com taxa de bits adaptável ao criar um localizador de streaming sob demanda e criar uma URL de transmissão. O tópico [codificando um ativo](media-services-encode-asset.md) mostra como codificar um conjunto de MP4 de taxa de bits adaptável. 

> [!NOTE]
> Se o seu conteúdo for criptografado, configure a política de entrega de ativos (conforme descrito [neste](media-services-dotnet-configure-asset-delivery-policy.md) tópico) antes de criar um localizador. 
> 
> 

Você também pode usar um localizador de streaming sob demanda para criar URLs que apontam para arquivos MP4 que podem ser baixados progressivamente.  

Este tópico mostra como criar um localizador de streaming sob demanda para publicar seu ativo e compilar um Smooth, MPEG DASH e URLs de streaming do HLS. Ele também mostra se mostra muito interessado em criar URLs de download progressivo. 

## <a name="create-an-ondemand-streaming-locator"></a>Criar um localizador de streaming sob demanda
Para criar o localizador de streaming sob demanda e obter URLs, você precisa fazer as seguintes coisas:

1. Se o conteúdo for criptografado, defina uma política de acesso.
2. Criar um localizador de streaming sob demanda.
3. Se você planeja transmitir, obtenha o arquivo de manifesto do streaming (.ism) no ativo. 
   
   Se você planeja fazer download progressivo, obtenha os nomes dos arquivos MP4 no ativo.  
4. Crie URLs para o arquivo de manifesto ou arquivos MP4. 


>[!NOTE]
>Há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política se estiver sempre usando os mesmos dias/permissões de acesso. Por exemplo, políticas de localizadores que devam permanecer no local por um longo período (políticas sem carregamento). Para obter mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="use-media-services-net-sdk"></a>Usar o SDK do .NET dos Serviços de Mídia
Criar URLs de streaming 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

As saídas:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Você também pode transmitir seu conteúdo por uma conexão SSL. Para seguir essa abordagem, certifique-se de que suas URLs de streaming começam com HTTPS. Atualmente, o AMS não dá suporte ao SSL com domínios personalizados.
> 
> 

Crie URLs de download progressivo 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
As saídas:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Usam o SDK do .NET dos Serviços de Mídia
O código a seguir chama métodos de extensões do SDK do .NET que criam um localizador e geram o Smooth Streaming, HLS e URLs do MPEG-DASH para streaming adaptável.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
* [Baixar ativos](media-services-deliver-asset-download.md)
* [Configurar política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md)

