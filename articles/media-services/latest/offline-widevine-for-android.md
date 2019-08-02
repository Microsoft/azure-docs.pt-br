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
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 9e90951f810c5101a46c29570af8ad71b42be637
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341025"
---
# <a name="offline-widevine-streaming-for-android"></a>Streaming offline do Widevine para Android

Além de proteger o conteúdo para streaming online, os serviços de aluguel e assinatura de conteúdo de mídia oferecem conteúdo para baixar que funciona quando você não está conectado à Internet. Talvez você precise baixar o conteúdo em seu telefone ou tablet para reprodução no modo avião quando estiver em um voo desconectado da rede. Outros cenários, em que talvez você deseje baixar o conteúdo:

- Alguns provedores de conteúdo pode não permitir a entrega de licença do DRM além das fronteiras de um país/região. Se um usuário quiser ver o conteúdo durante uma viagem para o exterior, é necessário fazer o download offline.
- Em alguns países ou regiões, a disponibilidade de Internet e/ou largura de banda é limitada. Os usuários podem optar por baixar o conteúdo para assistir em uma resolução alta o suficiente para uma experiência de exibição satisfatória.

Este artigo aborda como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android. O DRM offline permite que você forneça modelos de assinatura, aluguel e compra para seu conteúdo, possibilitando aos clientes de seus serviços levar o conteúdo consigo com facilidade quando estiverem desconectados da Internet.

Para criar aplicativos de player do Android, descrevemos três opções:

> [!div class="checklist"]
> * Criar um player usando a API do Java do SDK do ExoPlayer
> * Criar um player usando a associação do Xamarin do SDK do ExoPlayer
> * Criar um player usando EME (Extensão de Mídia Criptografada) e MSE (Extensão de Origem de Mídia) no navegador móvel Chrome v62 ou posterior

O artigo também responde a algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido pelo Widevine.

> [!NOTE]
> DRM offline é cobrado apenas para fazer uma única solicitação para uma licença ao baixar o conteúdo. Os erros não são cobrados.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de implementar o DRM offline para Widevine em dispositivos Android, primeiro você deverá:

- Familiarizar-se com os conceitos apresentados para a proteção de conteúdo online usando o DRM do Widevine. Isso é abordado em detalhes nos seguintes documentos/amostras:
    - [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)
    - [Use criptografia dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
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

A maneira mais fácil de desenvolver um aplicativo de player nativo para dispositivos Android é usar o [SDK do Google ExoPlayer](https://github.com/google/ExoPlayer), um SDK de player de vídeo de software livre. O ExoPlayer dá suporte a recursos que atualmente não são compatíveis com a API do MediaPlayer nativo do Android, incluindo os protocolos de entrega MPEG-DASH e Microsoft Smooth Streaming.

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

Confira também o seguinte thread: [Associação do Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplicativos de player do Chrome para Android

A partir do lançamento do [Chrome para Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), há suporte para licença persistente em EME. Agora, o [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) também é compatível com o Chrome para Android. Isso permite que você crie aplicativos de reprodução offline no Chrome caso os usuários finais tiverem essa versão (ou superior) do Chrome. 

Além disso, a Google produziu uma amostra de PWA (Aplicativo Web Progressivo), publicando-a como software livre: 

- [Código-fonte](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versão hospedada pelo Google](https://biograf-155113.appspot.com/ttt/episode-2/) (funciona apenas no Chrome v 62 e superior em dispositivos Android)

Se você fizer upgrade do navegador móvel Chrome para a v62 (ou superior) em um telefone Android e testar o aplicativo de exemplo hospedado acima, verá que o streaming online e a reprodução offline funcionam.

O aplicativo PWA de software livre acima foi criado no Node.js. Se deseja hospedar sua própria versão em um servidor Ubuntu, os seguintes problemas comuns encontrados que podem impedir a reprodução:

1. Problema do CORS: o exemplo de vídeo no aplicativo de exemplo hospedado em https://storage.googleapis.com/biograf-video-files/videos/. A Google configurou o CORS para todas as suas amostras de teste hospedadas no bucket do Google Cloud Storage. Elas trazem cabeçalhos do CORS, especificando explicitamente a entrada do CORS https://biograf-155113.appspot.com (domínio no qual a Google hospeda sua amostra), impedindo o acesso por outros sites. Se você tentar, verá o seguinte erro HTTP: Falha ao carregar https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado. Origem ' https:\//13.85.80.81:8080', portanto, não é permitido acessar. Se uma resposta opaca atender às suas necessidades, defina o modo da solicitação como 'no-cors' para buscar o recurso com o CORS desabilitado.
2. Problema de certificado: do Chrome v 58 em diante, o EME para Widevine exige HTTPS. Portanto, é necessário hospedar o aplicativo de exemplo em HTTPS com um certificado X509. Um certificado de teste normal não funciona devido aos seguintes requisitos: Você precisa obter um certificado que atenda aos seguintes requisitos mínimos:
    - O Chrome e Firefox exigem a presença da configuração de SAN (Nome Alternativo da Entidade) no certificado
    - O certificado precisa ter uma AC confiável e um certificado autoassinado de desenvolvimento não funciona
    - O certificado precisa ter uma correspondência entre o CN e o nome DNS do servidor Web ou do gateway

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="question"></a>Pergunta

Como fornecer licenças persistentes (habilitadas offline) para alguns clientes/usuários e licenças não persistentes (desabilitadas offline) para outros? É necessário duplicar o conteúdo e usar uma chave de conteúdo separada?

### <a name="answer"></a>Resposta
Uma vez que os Serviços de Mídia v3 permitem que um Ativo tenha vários StreamingLocators. Você pode ter

1.  Uma ContentKeyPolicy com o license_type = "persistent", ContentKeyPolicyRestriction com a declaração em "persistente", e seu StreamingLocator;
2.  Outra ContentKeyPolicy com o license_type="nonpersistent", ContentKeyPolicyRestriction com a declaração em "nonpersistent", e seu StreamingLocator.
3.  Os dois StreamingLocators têm uma ContentKey diferente.

Dependendo da lógica de negócios do STS personalizado, diferentes declarações são emitidas no token JWT. Com o token, apenas a licença correspondente pode ser obtida e apenas a URL correspondente pode ser reproduzida.

### <a name="question"></a>Pergunta

Em relação aos níveis de segurança do Widevine, na documentação [Visão geral da arquitetura de DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) da Google, são definidos três níveis de segurança diferentes. No entanto, na [documentação dos Serviços de Mídia do Azure no modelo de licença do Widevine](widevine-license-template-overview.md), são descritos cinco níveis de segurança diferentes. Qual é a relação ou o mapeamento entre os dois conjuntos diferentes de níveis de segurança?

### <a name="answer"></a>Resposta

Na [Visão geral da arquitetura de DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) da Google, são definidos os três seguintes níveis de segurança:

1.  Nível de Segurança 1: Todo processamento, criptografia e controle de conteúdo é realizado no TEE (Ambiente de Execução Confiável). Em alguns modelos de implementação, o processamento da segurança pode ser feito em diferentes chips.
2.  Nível de Segurança 2: Realiza a criptografia (mas não o processamento de vídeo) no TEE: os buffers descriptografados são retornados para o domínio do aplicativo e processados por meio de um hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas somente no TEE.
3.  Nível de Segurança 3: não tem um TEE no dispositivo. Podem ser tomadas medidas apropriadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de Nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança.

Ao mesmo tempo, na [documentação dos Serviços de Mídia do Azure no modelo de licença do Widevine](widevine-license-template-overview.md), a propriedade security_level de content_key_specs pode ter os cinco seguintes valores diferentes (requisitos de robustez do cliente para reprodução):

1.  A criptografia whitebox baseada em software é obrigatória.
2.  A criptografia de software e um decodificador oculto são obrigatórios.
3.  As operações de criptografia e material de chave precisam ser executadas em um TEE com suporte de hardware.
4.  A criptografia e a decodificação do conteúdo precisam ser feitas em um TEE com suporte de hardware.
5.  A criptografia, a decodificação e qualquer manipulação da mídia (compactada e descompactada) precisam ser feitas em um TEE com suporte de hardware.

Ambos os níveis de segurança são definidos pelo Google Widevine. A diferença está em seu nível de uso: nível da arquitetura ou nível da API. Os cinco níveis de segurança são usados na API do Widevine. O objeto content_key_specs, que contém security_level, é desserializado e passado para o serviço de entrega global do Widevine pelo serviço de licença do Widevine nos Serviços de Mídia do Azure. A tabela abaixo mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura do Widevine** |**Níveis de segurança usados na API do Widevine**|
|---|---| 
| **Nível de Segurança 1**: Todo processamento, criptografia e controle de conteúdo é realizado no TEE (Ambiente de Execução Confiável). Em alguns modelos de implementação, o processamento da segurança pode ser feito em diferentes chips.|**security_level=5**: A criptografia, a decodificação e qualquer manipulação da mídia (compactada e descompactada) precisam ser feitas em um TEE com suporte de hardware.<br/><br/>**security_level=4**: A criptografia e a decodificação do conteúdo precisam ser feitas em um TEE com suporte de hardware.|
**Nível de Segurança 2**: Realiza a criptografia (mas não o processamento de vídeo) no TEE: os buffers descriptografados são retornados para o domínio do aplicativo e processados por meio de um hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas somente no TEE.| **security_level=3**: As operações de criptografia e material de chave precisam ser executadas em um TEE com suporte de hardware. |
| **Nível de Segurança 3**: Não tem um TEE no dispositivo. Podem ser tomadas medidas apropriadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de Nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança. | **security_level=2**: A criptografia de software e um decodificador oculto são obrigatórios.<br/><br/>**security_level=1**: A criptografia whitebox baseada em software é obrigatória.|

### <a name="question"></a>Pergunta

Por que o download de conteúdo leva muito tempo?

### <a name="answer"></a>Resposta

Há duas maneiras para melhorar a velocidade do download:

1.  Habilitar a CDN, de modo que seja mais provável que os usuários finais acessem a CDN, em vez da origem e/ou do ponto de extremidade de streaming para o download de conteúdo. Se o usuário acessar o ponto de extremidade de streaming, cada segmento de HLS ou fragmento de DASH será empacotado e criptografado dinamicamente. Embora essa latência esteja na escala de milissegundos para cada segmento/fragmento, quando você tem um vídeo com duração de uma hora, a latência acumulada pode ser grande, levando a um download mais longo.
2.  Fornecer aos usuários finais a opção de baixar seletivamente camadas de qualidade de vídeo e faixas de áudio, em vez de todo o conteúdo. Para o modo offline, não faz sentido baixar todas as camadas de qualidade. Há duas maneiras de fazer isso:
    1.  Controlado pelo cliente: o aplicativo de player seleciona automaticamente ou o usuário seleciona a camada de qualidade de vídeo e as faixas de áudio a serem baixadas;
    2.  Controlado pelo serviço: é possível usar o recurso de Manifesto Dinâmico nos Serviços de Mídia do Azure para criar um filtro (global), que limita a playlist de HLS ou o DASH MPD a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, a URL de download apresentada aos usuários finais incluirá esse filtro.

## <a name="summary"></a>Resumo

Este artigo abordou como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android.  Também respondeu a algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido pelo Widevine.
