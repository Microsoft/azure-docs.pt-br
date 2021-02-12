---
title: Transmitir Widevine Android offline
description: Este tópico mostra como configurar sua conta dos serviços de mídia do Azure V3 para streaming offline de conteúdo protegido por Widevine.
services: media-services
keywords: DASH, DRM, Modo Offline do Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c7adab9c14075e12cf5656f9e798e78ff26ff0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955200"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Streaming Widevine offline para Android com os serviços de mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Além de proteger o conteúdo para streaming online, os serviços de aluguel e assinatura de conteúdo de mídia oferecem conteúdo para baixar que funciona quando você não está conectado à Internet. Talvez você precise baixar o conteúdo em seu telefone ou tablet para reprodução no modo avião quando estiver em um voo desconectado da rede. Outros cenários, em que talvez você deseje baixar o conteúdo:

- Alguns provedores de conteúdo podem impedir a entrega de licença do DRM além da borda de um país/região. Se um usuário quiser ver o conteúdo durante uma viagem para o exterior, é necessário fazer o download offline.
- Em alguns países/regiões, a disponibilidade e/ou a largura de banda da Internet é limitada. Os usuários podem optar por baixar o conteúdo para assistir em uma resolução alta o suficiente para uma experiência de exibição satisfatória.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Este artigo aborda como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android. O DRM offline permite que você forneça modelos de assinatura, aluguel e compra para seu conteúdo, possibilitando aos clientes de seus serviços levar o conteúdo consigo com facilidade quando estiverem desconectados da Internet.

Para criar aplicativos de player do Android, descrevemos três opções:

> [!div class="checklist"]
> * Criar um player usando a API do Java do SDK do ExoPlayer
> * Criar um player usando a associação do Xamarin do SDK do ExoPlayer
> * Criar um player usando EME (Extensão de Mídia Criptografada) e MSE (Extensão de Origem de Mídia) no navegador móvel Chrome v62 ou posterior

O artigo também responde a algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido pelo Widevine.

> [!NOTE]
> O DRM offline é cobrado apenas para fazer uma única solicitação de licença quando você baixa o conteúdo. Quaisquer erros não são cobrados.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de implementar o DRM offline para Widevine em dispositivos Android, primeiro você deverá:

- Familiarizar-se com os conceitos apresentados para a proteção de conteúdo online usando o DRM do Widevine. Isso é abordado em detalhes nos seguintes documentos/amostras:
    - [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)
    - [Usar o serviço de entrega de licença e criptografia dinâmica do DRM](protect-with-drm.md)
- Clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Você precisará modificar o código em [Criptografar com DRM usando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações do Widevine.  
- Familiarizar-se com o SDK do Google ExoPlayer para Android, um SDK de player de vídeo de software livre que pode dar suporte à reprodução de DRM offline do Widevine. 
    - [SDK do ExoPlayer](https://github.com/google/ExoPlayer)
    - [Guia do Desenvolvedor do ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog do Desenvolvedor do ExoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Configurar proteção de conteúdo nos Serviços de Mídia do Azure

No método [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189), as seguintes etapas necessárias estão presentes:

1. Especifique como a entrega de chave de conteúdo é autorizada no serviço de entrega de licença: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Configure o modelo de licença do Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Crie ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Habilitar modo offline

Para habilitar o modo **offline** de licenças do Widevine, você precisa configurar o [modelo de licença do Widevine](widevine-license-template-overview.md). No objeto **policy_overrides**, defina a propriedade **can_persist** como **true** (o padrão é false), conforme mostrado em [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Configurando o player do Android para reprodução offline

A maneira mais fácil de desenvolver um aplicativo de player nativo para dispositivos Android é usar o [SDK do Google ExoPlayer](https://github.com/google/ExoPlayer), um SDK de player de vídeo de software livre. O ExoPlayer dá suporte a recursos que não têm suporte atualmente pela API do MediaPlayer nativo do Android, incluindo os protocolos de entrega MPEG-DASH e Microsoft Smooth Streaming.

O ExoPlayer versão 2.6 e posterior inclui muitas classes que dão suporte à reprodução DRM offline do Widevine. Em particular, a classe OfflineLicenseHelper fornece funções de utilitário para facilitar o uso do DefaultDrmSessionManager para download, renovação e liberação de licenças offline. As classes fornecidas na pasta “library/core/src/main/java/com/google/android/exoplayer2/offline/” do SDK são compatíveis com download de conteúdo de vídeo offline.

A seguinte lista de classes facilita o modo offline no SDK do ExoPlayer para Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Os desenvolvedores devem referenciar o [Guia do Desenvolvedor do ExoPlayer](https://google.github.io/ExoPlayer/guide.html) e o [Blog do Desenvolvedor](https://medium.com/google-exoplayer) correspondente durante o desenvolvimento de um aplicativo. O Google não liberou uma implementação de referência nem um código de exemplo totalmente documentado para o aplicativo ExoPlayer que dá suporte ao Widevine offline no momento. Portanto, as informações são limitadas ao blog e ao guia do desenvolvedor. 

### <a name="working-with-older-android-devices"></a>Trabalhando com dispositivos Android mais antigos

Para alguns dispositivos Android mais antigos, é necessário definir valores para as seguintes propriedades **policy_overrides** (definidas no [modelo de licença do Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** e **license_duration_seconds**). Como alternativa, você pode defini-los como zero, o que significa duração infinita/ilimitada.  

Os valores precisam ser definidos para evitar um bug de estouro de inteiro. Para obter mais explicações sobre o problema, consulte https://github.com/google/ExoPlayer/issues/3150 e https://github.com/google/ExoPlayer/issues/3112. <br/>Se você não definir os valores explicitamente, serão atribuídos valores muito grandes para **PlaybackDurationRemaining** e **LicenseDurationRemaining** (por exemplo, 9223372036854775807, que é o valor positivo máximo para um inteiro de 64 bits). Como resultado, a licença do Widevine aparece expirada e, portanto, a descriptografia não ocorrerá. 

Esse problema não ocorre no Android 5.0 Lollipop ou posterior, pois o Android 5.0 é a primeira versão do Android, que foi projetada para dar suporte total ao ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) e a plataformas de 64 bits, enquanto o Android 4.4 KitKat foi originalmente projetado para dar suporte ao ARMv7 a plataformas de 32 bits como ocorre com outras versões mais antigas do Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Usando o Xamarin para criar um aplicativo de reprodução do Android

Encontre associações do Xamarin para o ExoPlayer usando os seguintes links:

- [Biblioteca de associações do Xamarin para a biblioteca do Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Associações do Xamarin para NuGet do ExoPlayer](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Além disso, consulte o seguinte thread: [associação do Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplicativos de player do Chrome para Android

A partir do lançamento do [Chrome para Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), há suporte para a licença persistente em eme. Agora, o [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) também é compatível com o Chrome para Android. Isso permite que você crie aplicativos de reprodução offline no Chrome caso os usuários finais tiverem essa versão (ou superior) do Chrome. 

Além disso, a Google produziu uma amostra de PWA (Aplicativo Web Progressivo), publicando-a como software livre: 

- [Código-fonte](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versão hospedada pelo Google](https://biograf-155113.appspot.com/ttt/episode-2/) (funciona apenas no Chrome v 62 e superior em dispositivos Android)

Se você fizer upgrade do navegador móvel Chrome para a v62 (ou superior) em um telefone Android e testar o aplicativo de exemplo hospedado acima, verá que o streaming online e a reprodução offline funcionam.

O aplicativo PWA de software livre acima foi criado no Node.js. Se deseja hospedar sua própria versão em um servidor Ubuntu, os seguintes problemas comuns encontrados que podem impedir a reprodução:

1. Problema de CORS: o exemplo de vídeo no aplicativo de exemplo hospedado em https://storage.googleapis.com/biograf-video-files/videos/. A Google configurou o CORS para todas as suas amostras de teste hospedadas no bucket do Google Cloud Storage. Elas trazem cabeçalhos do CORS, especificando explicitamente a entrada do CORS `https://biograf-155113.appspot.com` (domínio no qual a Google hospeda sua amostra), impedindo o acesso por outros sites. Se você tentar, verá o seguinte erro HTTP: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Problema de certificado: a partir do Chrome v 58, o EME para Widevine exige HTTPS. Portanto, é necessário hospedar o aplicativo de exemplo em HTTPS com um certificado X509. Um certificado de teste normal não funciona devido aos requisitos a seguir. É necessário obter um certificado que atenda aos seguintes requisitos mínimos:
    - O Chrome e Firefox exigem a presença da configuração de SAN (Nome Alternativo da Entidade) no certificado
    - O certificado precisa ter uma AC confiável e um certificado autoassinado de desenvolvimento não funciona
    - O certificado precisa ter uma correspondência entre o CN e o nome DNS do servidor Web ou do gateway

## <a name="faqs"></a>Perguntas frequentes

Para obter mais informações, consulte [perguntas frequentes do Widevine](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>Observações adicionais

O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Este artigo abordou como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android.  Também respondeu a algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido pelo Widevine.
