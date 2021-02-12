---
title: Configurar sua conta para streaming offline de conteúdo protegido pelo Widevine – Azure
description: Este tópico mostra como configurar sua conta dos Serviços de Mídia do Azure para streaming offline de conteúdo protegido pelo Widevine.
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
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: dc21523d158330d70adfbc33734abe3d49535804
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696288"
---
# <a name="offline-widevine-streaming-for-android"></a>Streaming offline do Widevine para Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/offline-widevine-for-android.md)
> * [Versão 2](offline-widevine-for-android.md)

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Além de proteger o conteúdo para streaming online, os serviços de aluguel e assinatura de conteúdo de mídia oferecem conteúdo para baixar que funciona quando você não está conectado à Internet. Talvez você precise baixar o conteúdo em seu telefone ou tablet para reprodução no modo avião quando estiver em um voo desconectado da rede. Outros cenários, em que talvez você deseje baixar o conteúdo:

- Alguns provedores de conteúdo podem impedir a entrega de licença do DRM além da borda de um país/região. Se um usuário quiser ver o conteúdo durante uma viagem para o exterior, é necessário fazer o download offline.
- Em alguns países/regiões, a disponibilidade e/ou a largura de banda da Internet é limitada. Os usuários podem optar por baixar o conteúdo para assistir em uma resolução alta o suficiente para uma experiência de exibição satisfatória.

Este artigo aborda como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android. O DRM offline permite que você forneça modelos de assinatura, aluguel e compra para seu conteúdo, possibilitando aos clientes de seus serviços levar o conteúdo consigo com facilidade quando estiverem desconectados da Internet.

Para criar aplicativos de player do Android, descrevemos três opções:

> [!div class="checklist"]
> * Criar um player usando a API do Java do SDK do ExoPlayer
> * Criar um player usando a associação do Xamarin do SDK do ExoPlayer
> * Criar um player usando EME (Extensão de Mídia Criptografada) e MSE (Extensão de Origem de Mídia) no navegador móvel Chrome v62 ou posterior

O artigo também responde a algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido pelo Widevine.

## <a name="requirements"></a>Requisitos 

Antes de implementar o DRM offline para Widevine em dispositivos Android, primeiro você deverá:

- Familiarizar-se com os conceitos apresentados para a proteção de conteúdo online usando o DRM do Widevine. Isso é abordado em detalhes nos seguintes documentos/amostras:
    - [Usar os Serviços de Mídia do Azure para fornecer licenças DRM ou chaves AES](media-services-deliver-keys-and-licenses.md)
    - [CENC com vários DRM e Controle de Acesso: design e implementação de referência no Azure e nos Serviços de Mídia do Azure](media-services-cenc-with-multidrm-access-control.md)
    - [Usando a Criptografia Comum Dinâmica do PlayReady e/ou do Widevine com o .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Usar os Serviços de Mídia do Azure para fornecer licenças do PlayReady e/ou do Widevine com o .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Familiarizar-se com o SDK do Google ExoPlayer para Android, um SDK de player de vídeo de software livre que pode dar suporte à reprodução de DRM offline do Widevine. 
    - [SDK do ExoPlayer](https://github.com/google/ExoPlayer)
    - [Guia do Desenvolvedor do ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog do Desenvolvedor do ExoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Configuração de proteção de conteúdo nos Serviços de Mídia do Azure

Ao configurar a proteção do Widevine de um ativo nos Serviços de Mídia, você precisa criar a ContentKeyAuthorizationPolicyOption que especificou os três seguintes itens:

1. Sistema DRM (Widevine)
2. ContentKeyAuthorizationPolicyRestriction, especificando como a distribuição de chaves de conteúdo é autorizada no serviço de distribuição de licenças (Open Authorization ou autorização de token)
3. Modelo de licença DRM (Widevine)

Para habilitar o modo **offline** de licenças do Widevine, você precisa configurar o [modelo de licença do Widevine](media-services-widevine-license-template-overview.md). No objeto **policy_overrides**, defina a propriedade **can_persist** como **true** (o padrão é falso). 

O exemplo de código a seguir usa o .NET para habilitar o modo **offline** em licenças do Widevine. O código se baseia na amostra [Usando a Criptografia Comum Dinâmica do PlayReady e/ou do Widevine com o .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

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

Para alguns dispositivos Android mais antigos, é necessário definir valores para as seguintes propriedades **policy_overrides** (definidas no [modelo de licença do Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** e **license_duration_seconds**). Como alternativa, você pode defini-los como zero, o que significa duração infinita/ilimitada.  

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

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="question"></a>Pergunta

Como fornecer licenças persistentes (habilitadas offline) para alguns clientes/usuários e licenças não persistentes (desabilitadas offline) para outros? É necessário duplicar o conteúdo e usar uma chave de conteúdo separada?

### <a name="answer"></a>Resposta
Não é necessário duplicar o conteúdo. Você pode simplesmente usar uma única cópia do conteúdo e uma única ContentKeyAuthorizationPolicy, mas duas ContentKeyAuthorizationPolicyOption separadas:

1. IContentKeyAuthorizationPolicyOption 1: usa a licença persistente e o ContentKeyAuthorizationPolicyRestriction 1 que contém uma declaração como license_type = "Persistent"
2. IContentKeyAuthorizationPolicyOption 2: usa licença não persistente e ContentKeyAuthorizationPolicyRestriction 2 que contém uma declaração como license_type = "não persistente"

Dessa forma, quando uma solicitação de licença é recebida do aplicativo cliente, não há nenhuma diferença na solicitação de licença. No entanto, para um usuário/dispositivo final diferente, o STS deve ter a lógica de negócios para emitir diferentes tokens JWT contendo declarações diferentes (uma das license_type duas opções acima). O valor das declarações no token JWT será usado pelo serviço de licença para decidir qual tipo de licença emitir: persistente ou não persistente.

Isso significa que o STS (Secure Token Service) precisa ter a lógica de negócios e as informações do cliente/dispositivo para adicionar a um token o valor de declaração correspondente.

### <a name="question"></a>Pergunta

Para os níveis de segurança do Widevine, na documentação do [documento visão geral da arquitetura DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) do Google, ele define três níveis de segurança diferentes. No entanto, na [documentação dos Serviços de Mídia do Azure no modelo de licença do Widevine](./media-services-widevine-license-template-overview.md), são descritos cinco níveis de segurança diferentes. Qual é a relação ou o mapeamento entre os dois conjuntos diferentes de níveis de segurança?

### <a name="answer"></a>Resposta

Na [visão geral da arquitetura de DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)do Google, ele define os três níveis de segurança a seguir:

1.  Nível de Segurança 1: todo o processamento, criptografia e controle de conteúdo é realizado no TEE (Ambiente de Execução Confiável). Em alguns modelos de implementação, o processamento da segurança pode ser feito em diferentes chips.
2.  Nível de Segurança 2: faz a criptografia (mas não o processamento de vídeo) no TEE: os buffers descriptografados são retornados para o domínio do aplicativo e processados por meio de um hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas somente no TEE.
3.  Nível de Segurança 3: não tem um TEE no dispositivo. Podem ser tomadas medidas apropriadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de Nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança.

Ao mesmo tempo, na [documentação dos Serviços de Mídia do Azure no modelo de licença do Widevine](./media-services-widevine-license-template-overview.md), a propriedade security_level de content_key_specs pode ter os cinco seguintes valores diferentes (requisitos de robustez do cliente para reprodução):

1.  A criptografia whitebox baseada em software é obrigatória.
2.  A criptografia de software e um decodificador oculto são obrigatórios.
3.  As operações de criptografia e material de chave precisam ser executadas em um TEE com suporte de hardware.
4.  A criptografia e a decodificação do conteúdo precisam ser feitas em um TEE com suporte de hardware.
5.  A criptografia, a decodificação e qualquer manipulação da mídia (compactada e descompactada) precisam ser feitas em um TEE com suporte de hardware.

Ambos os níveis de segurança são definidos pelo Google Widevine. A diferença está em seu nível de uso: nível da arquitetura ou nível da API. Os cinco níveis de segurança são usados na API do Widevine. O objeto content_key_specs, que contém security_level, é desserializado e passado para o serviço de entrega global do Widevine pelo serviço de licença do Widevine nos Serviços de Mídia do Azure. A tabela abaixo mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura do Widevine** |**Níveis de segurança usados na API do Widevine**|
|---|---| 
| **Nível de segurança 1**: todo o processamento, a criptografia e o controle de conteúdo são executados no sistema de execução confiável (desempenho). Em alguns modelos de implementação, o processamento da segurança pode ser feito em diferentes chips.|**security_level=5**: a criptografia, a decodificação e qualquer manipulação da mídia (compactada e descompactada) precisam ser feitas em um TEE com suporte de hardware.<br/><br/>**security_level=4**: a criptografia e a decodificação do conteúdo precisam ser feitas em um TEE com suporte de hardware.|
**Nível de Segurança 2**: faz a criptografia (mas não o processamento de vídeo) no TEE: os buffers descriptografados são retornados para o domínio do aplicativo e processados por meio de um hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas somente no TEE.| **security_level=3**: as operações de criptografia e material de chave precisam ser executadas em um TEE com suporte de hardware. |
| **Nível de Segurança 3**: não tem um TEE no dispositivo. Podem ser tomadas medidas apropriadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de Nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança. | **security_level=2**: a criptografia de software e um decodificador oculto são obrigatórios.<br/><br/>**security_level=1**: a criptografia whitebox baseada em software é obrigatória.|

### <a name="question"></a>Pergunta

Por que o download de conteúdo leva muito tempo?

### <a name="answer"></a>Resposta

Há duas maneiras para melhorar a velocidade do download:

1.  Habilitar a CDN, de modo que seja mais provável que os usuários finais acessem a CDN, em vez da origem e/ou do ponto de extremidade de streaming para o download de conteúdo. Se o usuário acessar o ponto de extremidade de streaming, cada segmento de HLS ou fragmento de DASH será empacotado e criptografado dinamicamente. Embora essa latência esteja na escala de milissegundos para cada segmento/fragmento, quando você tem um vídeo com duração de uma hora, a latência acumulada pode ser grande, levando a um download mais longo.
2.  Fornecer aos usuários finais a opção de baixar seletivamente camadas de qualidade de vídeo e faixas de áudio, em vez de todo o conteúdo. Para o modo offline, não faz sentido baixar todas as camadas de qualidade. Há duas maneiras de fazer isso:
    1.  Controlado pelo cliente: o aplicativo de player seleciona automaticamente ou o usuário seleciona a camada de qualidade de vídeo e as faixas de áudio a serem baixadas;
    2.  Controlado pelo serviço: é possível usar o recurso de Manifesto Dinâmico nos Serviços de Mídia do Azure para criar um filtro (global), que limita a playlist de HLS ou o DASH MPD a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, a URL de download apresentada aos usuários finais incluirá esse filtro.

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Este artigo abordou como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android.  Também respondeu a algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido pelo Widevine.
