---
title: Perguntas frequentes sobre os serviços de mídia do Azure v3
description: Este artigo fornece respostas para perguntas frequentes sobre os serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a452a056ff2bdbad5d2e461716ee1a56d36c8523
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897553"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Perguntas frequentes sobre os serviços de mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo fornece respostas para perguntas frequentes sobre os serviços de mídia do Azure v3.

## <a name="general"></a>Geral

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Quais são as limitações de portal do Azure para os serviços de mídia v3?

Você pode usar o [portal do Azure](https://portal.azure.com/) para gerenciar eventos ao vivo v3, exibir ativos e trabalhos v3, obter informações sobre como acessar APIs, criptografar conteúdo. <br/>Para todas as outras tarefas de gerenciamento (por exemplo, gerenciamento de transformações e trabalhos ou análise de conteúdo v3), use a [API REST](/rest/api/media/accountfilters), a [CLI](/cli/azure/ams)ou um dos [SDKs](media-services-apis-overview.md#sdks)com suporte.

Se o vídeo foi carregado anteriormente na conta dos Serviços de Mídia por meio da API dos Serviços de Mídia v3 ou se o conteúdo foi gerado com base em uma saída dinâmica, você não verá os botões **Codificar**, **Analisar** ou **Criptografar** no portal do Azure. Use as APIs dos Serviços de Mídia v3 para executar essas tarefas.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quais funções do Azure podem executar ações nos recursos dos serviços de mídia do Azure? 

Consulte [controle de acesso baseado em função do Azure (RBAC do Azure) para contas de serviços de mídia](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Como fazer Stream para dispositivos iOS da Apple?

Verifique se você tem **(Format = M3U8-AAPL)** no final do seu caminho (depois da parte **/manifest** da URL) para informar ao servidor de origem de streaming para retornar o conteúdo de http Live streaming (hls) para consumo em dispositivos IOS nativos da Apple. Para obter detalhes, consulte [entregando conteúdo](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Como fazer para configurar Unidades Reservadas para Mídia?

Para trabalhos de análise de vídeo e análise de áudio disparados pelos serviços de mídia V3 ou Video Indexer, recomendamos que você provisione sua conta com 10 MRUs (unidades reservadas de mídia) S3. Se você precisar de mais de 10 S3 MRUs, abra um tíquete de suporte usando o [portal do Azure](https://portal.azure.com/).

Para obter detalhes, consulte [escala de processamento de mídia](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Use [Transformações](/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada Transformação descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Um [trabalho](/rest/api/media/jobs) é a solicitação real aos serviços de mídia para aplicar a transformação a um vídeo de entrada ou conteúdo de áudio. Depois que a transformação tiver sido criada, você poderá enviar trabalhos usando as APIs dos serviços de mídia ou qualquer um dos SDKs publicados. Para obter mais informações, consulte [Transformações e Trabalhos](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>carreguei, codifiquei e publiquei um vídeo. Por que o vídeo não toca quando tento transmiti-lo?

Um dos motivos mais comuns é que você não tem o ponto de extremidade de streaming do qual está tentando reproduzir no estado de execução.

### <a name="how-does-pagination-work"></a>Como a paginação funciona?

Quando você estiver usando a paginação, sempre deverá usar o link seguinte para enumerar a coleção e não depender de um tamanho de página específico. Para obter detalhes e exemplos, confira [Filtragem, ordenação, paginação](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais recursos ainda não estão disponíveis nos serviços de mídia do Azure v3?

Para obter detalhes, consulte [o guia de migração](migrate-v-2-v-3-migration-introduction.md).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de mover uma conta de serviços de mídia entre assinaturas?  

Para obter detalhes, consulte [movendo uma conta dos serviços de mídia entre assinaturas](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão ao vivo 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Como fazer interromper a transmissão ao vivo depois que a difusão for concluída?

Você pode abordá-lo do lado do cliente ou do lado do servidor.

#### <a name="client-side"></a>Lado do cliente

Seu aplicativo Web deve solicitar ao usuário que ele determine a transmissão enquanto estiver fechando o navegador. Este é um evento de navegador que seu aplicativo Web pode manipular.

#### <a name="server-side"></a>Lado do Servidor

Você pode monitorar eventos ao vivo assinando os eventos da grade de eventos do Azure. Para obter mais informações, consulte o [esquema de evento EventGrid](media-services-event-schemas.md#live-event-types).

Você pode:

* [Assine](reacting-to-media-services-events.md) os eventos [Microsoft. Media. LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) de nível de fluxo e monitore que nenhuma reconexão chega por um tempo para parar e excluir seu evento ao vivo.
* [Assine](reacting-to-media-services-events.md) os eventos de [pulsação](media-services-event-schemas.md#liveeventingestheartbeat) no nível de rastreamento. Se todas as faixas tiverem uma queda de taxa de bits de entrada para 0 ou o último carimbo de data/hora não estiver mais aumentando, você poderá desligar com segurança o evento ao vivo. Os eventos de pulsação entram a cada 20 segundos para cada faixa, portanto, pode ser um pouco detalhado.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Como fazer inserir quebras/vídeos e slates de imagem durante uma transmissão ao vivo?

A codificação ativa dos Serviços de Mídia v3 ainda não dá suporte à inserção de vídeo ou de imagens fixas durante a transmissão ao vivo. 

Você pode usar um [codificador ativo local](recommended-on-premises-live-encoders.md) para alternar para o vídeo de origem. Muitos aplicativos fornecem a capacidade de alternar fontes, incluindo o Telestream Wirecast, o switch Studio (no iOS) e o OBS Studio (aplicativo gratuito).

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar criptografia de chave não criptografada AES-128 ou um sistema DRM?

Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida para o cliente via TLS para que a chave seja criptografada em trânsito, mas sem nenhuma criptografia adicional ("em claro"). Como resultado, a chave usada para descriptografar o conteúdo é acessível ao Player do cliente e pode ser exibida em um rastreamento de rede no cliente em texto sem formatação. A criptografia de chave AES-128 não criptografada é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem visualizados pelos funcionários).

Os sistemas DRM, como PlayReady, Widevine e FairPlay, fornecem um nível adicional de criptografia na chave usada para descriptografar o conteúdo, em comparação com uma chave de limpeza AES-128. A chave de conteúdo é criptografada para uma chave protegida pelo tempo de execução do DRM, além de qualquer criptografia de nível de transporte fornecida pelo TLS. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. Recomendamos o DRM para casos de uso em que o Visualizador pode não ser uma parte confiável e você precisa do nível mais alto de segurança.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como fazer mostrar um vídeo apenas aos usuários que têm uma permissão específica, sem usar o Azure AD?

Você não precisa usar nenhum provedor de token específico, como Azure Active Directory (Azure AD). Você pode criar seu próprio provedor de [JWT](https://jwt.io/) (chamado de serviço de token seguro ou STS) usando a criptografia de chave assimétrica. Em seu STS personalizado, você pode adicionar declarações com base em sua lógica de negócios.

Certifique-se de que o emissor, o público e as declarações correspondam exatamente entre o que está em JWT e o `ContentKeyPolicyRestriction` valor usado em `ContentKeyPolicy` .

Para obter mais informações, consulte [proteger seu conteúdo usando a criptografia dinâmica dos serviços de mídia](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Como e onde eu obtive um token JWT antes de usá-lo para solicitar uma licença ou chave?

Para produção, você precisa ter um serviço de token seguro (ou seja, um serviço Web), que emite um token JWT após uma solicitação HTTPS. Para teste, você pode usar o código mostrado no `GetTokenAsync` método definido em [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

O Player faz uma solicitação, depois que um usuário é autenticado, para o STS para esse token e o atribui como o valor do token. Você pode usar o [API do Player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

Para obter um exemplo de como executar o STS com uma chave simétrica ou uma chave assimétrica, consulte a [ferramenta JWT](https://aka.ms/jwt). Para obter um exemplo de um player baseado em Player de Mídia do Azure usando um token JWT, consulte a [ferramenta de teste de mídia do Azure](https://aka.ms/amtest). (Expanda o link **player_settings** para ver a entrada do token.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como fazer autorizar solicitações para transmitir vídeos com criptografia AES?

A abordagem correta é usar o Secure token Service. No STS, dependendo do perfil do usuário, adicione declarações diferentes (como "usuário Premium", "usuário de avaliação gratuita"). Com diferentes declarações JWT, o usuário pode ver é diferente do conteúdo. Para conteúdos ou ativos diferentes, terá `ContentKeyPolicyRestriction` o valor correspondente `RequiredClaims` .

Use as APIs dos serviços de mídia do Azure para configurar a entrega de licença/chave e criptografar seus ativos (conforme mostrado neste [exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Para obter mais informações, consulte:

- [Visão geral de proteção do conteúdo](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Devo usar HTTP ou HTTPS?
O aplicativo de player ASP.NET MVC deve ser compatível com o seguinte:

* Autenticação de usuário por meio do Azure AD que está em HTTPS.
* Troca de JWT entre o cliente e o Azure AD, que está em HTTPS.
* Aquisição de licença de DRM pelo cliente, que deve estar em HTTPS se a entrega de licença é fornecida pelos Serviços de Mídia. O pacote de produtos PlayReady não faz com que HTTPS seja obrigatório para a entrega de licença. Se o servidor de licença do PlayReady está fora dos Serviços de Mídia, você pode usar HTTP ou HTTPS.

O aplicativo de player ASP.NET usa HTTPS como uma melhor prática, portanto o Player de Mídia é uma página em HTTPS. No entanto, o HTTP é preferencial para streaming, portanto, você precisa considerar esses problemas com conteúdo misto:

* O navegador não permite conteúdo misto. Mas plug-ins como o Silverlight e o plug-in OSMF para Smooth e DASH permitem isso. O conteúdo misto é uma preocupação de segurança devido à ameaça da capacidade de injetar JavaScript mal-intencionado, o que pode colocar os dados do cliente em risco. Os navegadores bloqueiam essa funcionalidade por padrão. A única maneira de contornar isso é no lado do servidor (origem), por meio da permissão de todos os domínios (sejam HTTPS ou HTTP). Isso provavelmente também não será uma boa ideia.
* Evite conteúdo misto. O aplicativo de player e o Player de Mídia devem usar HTTP ou HTTPS. Quando você está executando conteúdo misto, a tecnologia Silverlightss requer a limpeza de um aviso de conteúdo misto. A tecnologia Flashss lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto de extremidade de streaming tiver sido criado antes de agosto de 2014, ele não será compatível com HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E quanto à Transmissão ao Vivo?

Você pode usar exatamente o mesmo design e implementação para ajudar a proteger a transmissão ao vivo nos serviços de mídia, tratando o ativo associado a um programa como um ativo VOD. Para fornecer uma proteção de vários DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de associar o ativo à saída dinâmica.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Mídia?

Muitas vezes, os clientes investiram em um farm de servidores de licenças em seu próprio Datacenter ou em um hospedado por provedores de serviços de DRM. Com a Proteção de Conteúdo dos Serviços de Mídia, você pode operar em modo híbrido. O conteúdo pode ser hospedado e protegido dinamicamente nos serviços de mídia, enquanto as licenças DRM são entregues por servidores fora dos serviços de mídia. Nesse caso, considere as seguintes alterações:

* O STS precisa emitir tokens que sejam aceitáveis e possam ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pela Axinom exigem um JWT específico que contém uma mensagem de autorização. Você precisa ter um STS para emitir esse JWT. 
* Você não precisa mais configurar o serviço de entrega de licença nos Serviços de Mídia. Você precisa fornecer as URLs de aquisição de licença (para PlayReady, Widevine e FairPlay) ao configurar o `ContentKeyPolicy` .

> [!NOTE]
> O Widevine é um serviço fornecido pelo Google e está sujeito aos termos de serviço e à política de privacidade do Google.

## <a name="media-services-v2-vs-v3"></a>Serviços de mídia v2 versus v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Eu posso usar o portal do Azure para gerenciar recursos da v3?

No momento, você pode usar o [portal do Azure](https://portal.azure.com/) para:

* Gerenciar [eventos ao vivo](live-events-outputs-concept.md) nos serviços de mídia v3. 
* Exibir (não gerenciar) os [ativos](assets-concept.md)v3. 
* [Obtenha informações sobre como acessar APIs](./access-api-howto.md). 

Para todas as outras tarefas de gerenciamento (por exemplo, [transformações e trabalhos](transforms-jobs-concept.md) e [proteção de conteúdo](content-protection-overview.md)), use a [API REST](/rest/api/media/), a [CLI do Azure](/cli/azure/ams)ou um dos [SDKs](media-services-apis-overview.md#sdks)com suporte.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile no v3?

O `AssetFile` conceito foi removido da API dos serviços de mídia para separar os serviços de mídia da dependência do SDK de armazenamento. Agora, o armazenamento do Azure, não os serviços de mídia, mantém as informações que pertencem ao SDK de armazenamento. 

Para obter mais informações, confira [Migrar para os Serviços de Mídia v3](migrate-v-2-v-3-migration-introduction.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde está a criptografia de armazenamento do lado do cliente?

Agora, recomendamos que você use a criptografia de armazenamento do lado do servidor (que está ativada por padrão). Para obter mais informações, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md).

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>Streaming do FairPlay para iOS

As perguntas frequentes a seguir fornecem assistência com a solução de problemas de streaming de FairPlay offline para iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Por que apenas o áudio toca, mas nenhum vídeo aparece durante o modo offline?

Esse comportamento parece estar relacionado ao design do aplicativo de exemplo. Quando uma faixa de áudio alternativa está presente (que é o caso de HLS) durante o modo offline, o iOS 10 e o iOS 11 têm como padrão a faixa de áudio alternativa. Para compensar esse comportamento para o modo offline de FPS, remova a faixa de áudio alternativa do fluxo. Para fazer isso nos serviços de mídia, adicione o filtro de manifesto dinâmico **-somente áudio = falso**. Em outras palavras, uma URL HLS termina com **. ISM/manifest (Format = M3U8-AAPL, áudio-only = false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Por que ela ainda toca áudio somente sem vídeo durante o modo offline após eu adicionar audio-only=false?

Dependendo do design da chave de cache da rede de distribuição de conteúdo, o conteúdo pode ser armazenado em cache. Limpe o cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>O modo offline de FPS tem suporte no iOS 11 além do iOS 10?

Sim. O modo offline de FPS tem suporte para iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Por que eu não consigo encontrar o documento “Reprodução Offline com o Streaming de FairPlay e HTTP Live Streaming no SDK do Servidor de FPS”?

Desde a versão 4 do SDK do Servidor de FPS, este documento foi mesclado no “Guia de Programação de Streaming de FairPlay”.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>O que é a estrutura de arquivos offline/baixados em dispositivos iOS?

A estrutura do arquivo baixado em um dispositivo iOS tem a aparência da captura de tela abaixo. A pasta `_keys` armazena licenças de FPS baixadas, com um arquivo de armazenamento para cada host de serviço de licença. A pasta `.movpkg` armazena conteúdo de áudio e vídeo. 

A primeira pasta com um nome que termina com um traço seguido por um número contém conteúdo de vídeo. O valor numérico é a largura de banda de pico das representações de vídeo. A segunda pasta com um nome que termina com um traço seguido por 0 contém o conteúdo de áudio. A terceira pasta chamada `Data` contém a lista de reprodução mestre do conteúdo de fps. Por fim, boot.xml fornece uma descrição completa do conteúdo da pasta `.movpkg`. 

![Estrutura de arquivos offline para o aplicativo de exemplo FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Aqui está um arquivo de boot.xml de exemplo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Streaming do Widevine para Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Como posso entregar licenças persistentes (offline habilitadas) para alguns clientes/usuários e licenças não persistentes (desabilitadas offline) para outras? É necessário duplicar o conteúdo e usar chaves de conteúdo separadas?

Como o Media Services V3 permite que um ativo tenha várias `StreamingLocator` instâncias, você pode ter:

* Uma `ContentKeyPolicy` instância com `license_type = "persistent"` , `ContentKeyPolicyRestriction` com Claim on e `"persistent"` seu `StreamingLocator` .
* Outra `ContentKeyPolicy` instância com `license_type="nonpersistent"` , `ContentKeyPolicyRestriction` com Claim on `"nonpersistent` "e sua `StreamingLocator` .
* Duas `StreamingLocator` instâncias que têm `ContentKey` valores diferentes.

Dependendo da lógica de negócios do STS personalizado, diferentes declarações são emitidas no token JWT. Com o token, apenas a licença correspondente pode ser obtida e apenas a URL correspondente pode ser reproduzida.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual é o mapeamento entre os níveis de segurança de DRM dos serviços de mídia e Widevine?

A "visão geral da arquitetura do Widevine DRM" do Google define três níveis de segurança. No entanto, a [documentação dos serviços de mídia do Azure no modelo de licença do Widevine](widevine-license-template-overview.md) descreve cinco níveis de segurança (requisitos de robustez do cliente para reprodução). Esta seção explica como os níveis de segurança são mapeados.

Os dois conjuntos de níveis de segurança são definidos pelo Google Widevine. A diferença está no nível de uso: arquitetura ou API. Os cinco níveis de segurança são usados na API do Widevine. O `content_key_specs` objeto, que contém `security_level` , é desserializado e passado para o serviço de entrega global do Widevine pelo serviço de licença do Widevine dos serviços de mídia do Azure. A tabela a seguir mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura Widevine** |**Níveis de segurança usados na API do Widevine**|
|---|---| 
| **Nível de segurança 1**: todo o processamento, a criptografia e o controle de conteúdo são executados no sistema de execução confiável (desempenho). Em alguns modelos de implementação, o processamento de segurança pode ser executado em diferentes chips.|**security_level = 5**: a criptografia, a decodificação e todas as manipulações da mídia (compactadas e descompactadas) devem ser tratadas em um t com suporte de hardware.<br/><br/>**security_level = 4**: a criptografia e a decodificação de conteúdo devem ser executadas em um "t" com suporte para hardware.|
**Nível de segurança 2**: a criptografia (mas não o processamento de vídeo) é executada no "t". Buffers descriptografados são retornados ao domínio do aplicativo e processados por meio de hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas apenas dentro do "t".| **security_level = 3**: as operações de criptografia e material de chave devem ser executadas em um alto desempenho com suporte de hardware. |
| **Nível de segurança 3**: não há nenhum "t" no dispositivo. As medidas apropriadas podem ser tomadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança. | **security_level = 2**: a criptografia de software e um decodificador ofuscado são necessários.<br/><br/>**security_level = 1**: a criptografia de caixa branca baseada em software é necessária.|

#### <a name="why-does-content-download-take-so-long"></a>Por que o download de conteúdo leva muito tempo?

Há duas maneiras para melhorar a velocidade do download:

* Habilite uma rede de distribuição de conteúdo para que os usuários tenham mais probabilidade de atingir isso em vez do ponto de extremidade de origem/streaming para download de conteúdo. Se um usuário atingir um ponto de extremidade de streaming, cada segmento HLS ou fragmento de traço será empacotado e criptografado dinamicamente. Embora essa latência esteja em escala de milissegundos para cada segmento ou fragmento, quando você tem um vídeo de duração de hora, a latência acumulada pode ser grande e causar um download mais longo.
* Conceda aos usuários a opção de baixar seletivamente camadas de qualidade de vídeo e faixas de áudio em vez de todo o conteúdo. Para o modo offline, não há nenhum ponto no download de todas as camadas de qualidade. Há duas maneiras de fazer isso:

  * Controlado pelo cliente: o aplicativo de Player seleciona automaticamente ou o usuário seleciona, a camada de qualidade de vídeo e as faixas de áudio para download.
  * Controlado pelo serviço: você pode usar o recurso de manifesto dinâmico nos serviços de mídia do Azure para criar um filtro (global), que limita a lista de reprodução HLS ou o DASH MPD a uma única camada de qualidade de vídeo e a faixas de áudio selecionadas. Em seguida, a URL de download apresentada aos usuários incluirá esse filtro.

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)