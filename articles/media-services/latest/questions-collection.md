---
title: Coleção de perguntas sobre os Serviços de Mídia do Azure v3
description: Este artigo apresenta respostas a uma coleção de perguntas sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: dc8f8f7ced1c5915c2ea54390685806cfcdd257f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280302"
---
<!-- NOTE this file is temporary and a placeholder until the FAQ file update is completed. -->

# <a name="media-services-v3-questions-collection"></a>Coleção de perguntas dos Serviços de Mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo apresenta respostas às perguntas frequentes sobre os Serviços de Mídia do Azure v3.

## <a name="general"></a>Geral

### <a name="does-media-services-store-any-customer-data-outside-of-the-service-region"></a>Os Serviços de Mídia armazenam os dados de clientes fora da região de serviço?

- Os clientes anexam as próprias contas de armazenamento à conta dos Serviços de Mídia do Azure.  Todos os dados de ativos são armazenados nessas contas de armazenamento associadas, e o cliente controla a localização e o tipo de replicação do armazenamento.
- Outros dados associados à conta dos Serviços de Mídia (incluindo chaves de criptografia de conteúdo, chaves de verificação de token, URLs de JobInputHttp e outros metadados de entidade) são guardados em armazenamento de propriedade da Microsoft na mesma região selecionada para a conta.
    - Devido aos [requisitos de residência de dados](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) no Sul do Brasil e no Sudeste da Ásia, os dados de conta adicionais são armazenados com redundância de zona e contidos em uma região. No Sudeste da Ásia, todos os dados de conta adicionais são armazenados em Singapura, e no Sul do Brasil, os dados são armazenados no Brasil.
    - Em outras regiões além do Sul do Brasil e do Sudeste da Ásia, pode haver armazenamento dos dados de conta adicionais no armazenamento de propriedade da Microsoft na [região emparelhada](../../best-practices-availability-paired-regions.md).
- Os Serviços de Mídia do Azure são um serviço regional, que não oferecem [alta disponibilidade](architecture-high-availability-encoding-concept.md) nem replicação de dados. Aos clientes que precisam desses recursos, recomendamos criar uma solução com contas dos Serviços de Mídia em várias regiões.  Há um guia disponível que mostra como criar uma solução para alta disponibilidade com o vídeo por demanda dos Serviços de Mídia.

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Quais são as limitações do portal do Azure para os Serviços de Mídia v3?

Você pode usar o [portal do Azure](https://portal.azure.com/) para gerenciar eventos ao vivo v3, ver ativos e trabalhos de v3, obter informações sobre o acesso a APIs e criptografar conteúdo. <br/>Para as demais tarefas administrativas (por exemplo, gerenciar transformações e trabalhos ou analisar conteúdo de v3), use a [API REST](/rest/api/media/accountfilters), a [CLI](/cli/azure/ams) ou um dos [SDKs](media-services-apis-overview.md#sdks) com suporte.

Se o vídeo foi carregado anteriormente na conta dos Serviços de Mídia por meio da API dos Serviços de Mídia v3 ou se o conteúdo foi gerado com base em uma saída dinâmica, você não verá os botões **Codificar**, **Analisar** ou **Criptografar** no portal do Azure. Use as APIs dos Serviços de Mídia v3 para executar essas tarefas.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Quais funções do Azure podem fazer ações nos recursos dos Serviços de Mídia do Azure? 

Confira [RBAC (controle de acesso baseado em função) do Azure para contas de Serviços de Mídia](security-rbac-concept.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Como fazer streaming para dispositivos iOS da Apple?

Insira **(format=m3u8-aapl)** ao final do caminho (após a parte **/manifest** da URL) para informar ao servidor de origem de streaming que envie conteúdo de HLS (HTTP Live Streaming) para consumo em dispositivos nativos iOS da Apple. Veja mais detalhes em [Entregando conteúdo](encode-dynamic-packaging-concept.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Use [Transformações](/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada Transformação descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. O [trabalho](/rest/api/media/jobs) é a solicitação aos Serviços de Mídia para que eles apliquem a transformação ao conteúdo de áudio ou vídeo de entrada. Após a criação da transformação, você pode enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. Para obter mais informações, consulte [Transformações e Trabalhos](transform-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>carreguei, codifiquei e publiquei um vídeo. Por que o vídeo não é reproduzido quando faço streaming?

Um dos motivos mais comuns é que o ponto de extremidade de streaming do qual você está tentando reproduzir não está no estado de execução.

### <a name="how-does-pagination-work"></a>Como a paginação funciona?

Ao usar a paginação, você sempre deve usar o próximo link para enumerar a coleção e não depender de um tamanho de página em particular. Para obter detalhes e exemplos, confira [Filtragem, ordenação, paginação](filter-order-page-entitites-how-to.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais recursos ainda não estão disponíveis nos Serviços de Mídia do Azure v3?

Veja mais detalhes no [Guia de migração](migrate-v-2-v-3-migration-introduction.md).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Como mover uma conta de uma assinatura a outra dos Serviços de Mídia?  

Veja mais detalhes em [Mover uma conta de uma assinatura a outra dos Serviços de Mídia](account-move-account-how-to.md).

## <a name="live-streaming"></a>Transmissão ao vivo 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Como parar a transmissão ao vivo após o fim da difusão?

Você pode abordar a questão pelo lado do cliente ou pelo lado do servidor.

#### <a name="client-side"></a>Lado do cliente

Quando o usuário fecha o navegador, o aplicativo Web deve pedir que ele confirme que deseja interromper a difusão. Esse é um evento de navegador que o aplicativo Web pode processar.

#### <a name="server-side"></a>Lado do Servidor

Você pode assinar os eventos da Grade de Eventos do Azure para acompanhar eventos ao vivo. Veja mais informações em [Esquema de evento da Grade de Eventos](monitoring/media-services-event-schemas.md#live-event-types).

Você pode:

* [Assinar](monitoring/reacting-to-media-services-events.md) os eventos de nível de streaming [Microsoft.Media.LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md#liveeventencoderdisconnected) e interromper e excluir o evento ao vivo após algum tempo sem reconexões.
* [Assinar](monitoring/reacting-to-media-services-events.md) os eventos de [pulsação](monitoring/media-services-event-schemas.md#liveeventingestheartbeat) no nível de rastreamento. Se a taxa de bits de todas as faixas cair a 0 ou o último carimbo de data/hora parar de aumentar, você poderá desligar com segurança o evento ao vivo. Os eventos de pulsação de todas as faixas entram a cada 20 segundos, portanto, isso pode ser detalhado demais.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Como inserir quebras/vídeos e imagens fixas durante a transmissão ao vivo?

A codificação ativa dos Serviços de Mídia v3 ainda não dá suporte à inserção de vídeo ou de imagens fixas durante a transmissão ao vivo. 

Você pode usar um [codificador ativo local](encode-recommended-on-premises-live-encoders.md) para alternar para o vídeo de origem. Há vários aplicativos em que você pode trocar a fonte, como o Telestream Wirecast, o Switcher Studio (no iOS) e o OBS Studio (aplicativo gratuito).

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar criptografia de chave não criptografada AES-128 ou um sistema de DRM (gerenciamento de direitos digitais)?

Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida ao cliente via TLS, de modo que ela é criptografada em trânsito, mas sem criptografia adicional. Por isso, o player do cliente tem acesso à chave usada para descriptografar o conteúdo, que é exibida em rastreamentos de rede no cliente em texto sem formatação. A criptografia de chave AES-128 não criptografada é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem visualizados pelos funcionários).

Os sistemas de DRM, como o PlayReady, o Widevine e o FairPlay, oferecem maior criptografia na chave usada para descriptografar o conteúdo, em comparação à chave AES-128 sem criptografia. A chave de conteúdo é criptografada com uma chave protegida pelo runtime do DRM, além da criptografia de nível de transporte do TLS. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. Recomendamos o DRM para casos de uso em que o espectador não é confiável e você precisa do máximo nível de segurança.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como mostrar um vídeo apenas aos usuários que têm uma certa permissão, sem usar o Azure AD?

Você não precisa usar nenhum provedor de token, como o Azure AD (Azure Active Directory). Você pode criar o seu provedor de [JWT](https://jwt.io/) (chamado de Serviço de Token Seguro ou STS) com a criptografia de chave assimétrica. No STS personalizado, você pode adicionar declarações com base em sua lógica de negócios.

O emissor, o público e as declarações devem ser exatamente iguais no JWT e no valor do `ContentKeyPolicyRestriction` usado no `ContentKeyPolicy`.

Veja mais informações em [Proteger conteúdo usando a criptografia dinâmica dos Serviços de Mídia](drm-content-protection-concept.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Como e onde obter o token de JWT para usá-lo para a licença de solicitação ou a chave?

Para a produção, você precisa ter um STS (ou seja, um serviço Web) que emite o token de JWT após uma solicitação HTTPS. Para teste, você pode usar o código mostrado no método `GetTokenAsync` definido em [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Após a autenticação do usuário, o player solicita o token ao STS e o atribui como o valor do token. Você pode usar o [API do Player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

Veja um exemplo da execução do STS com uma chave simétrica ou uma chave assimétrica em [Ferramenta JWT](https://aka.ms/jwt). Veja um exemplo de player baseado no Player de Mídia do Azure que usa o token do JWT na [ferramenta de teste de mídia do Azure](https://aka.ms/amtest). Expanda o link **player_settings** para ver a entrada do token.

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autorizar solicitações de transmissão de vídeos com a criptografia AES?

A abordagem correta é usar o STS. No STS, dependendo do perfil do usuário, adicione declarações diferentes (como "Usuário Premium", "Usuário Básico", "Usuário de Avaliação Gratuita"). Com diferentes declarações JWT, o usuário pode ver é diferente do conteúdo. Para conteúdos ou ativos diferentes, `ContentKeyPolicyRestriction` terá o valor correspondente `RequiredClaims`.

Use APIs de Serviços de Mídia do Azure para configurar a entrega de licença/chave e criptografar seus ativos (como mostra [este exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Para obter mais informações, consulte:

- [Visão geral de proteção do conteúdo](drm-content-protection-concept.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](architecture-design-multi-drm-system.md)

### <a name="should-i-use-http-or-https"></a>Devo usar HTTP ou HTTPS?
O aplicativo de player ASP.NET MVC deve ser compatível com o seguinte:

* Autenticação de usuário por meio do Azure AD que está em HTTPS.
* Troca de JWT entre o cliente e o Azure AD, que está em HTTPS.
* Aquisição de licença de DRM pelo cliente, que deve estar em HTTPS se a entrega de licença é fornecida pelos Serviços de Mídia. O pacote de produtos PlayReady não faz com que HTTPS seja obrigatório para a entrega de licença. Se o servidor de licença do PlayReady está fora dos Serviços de Mídia, você pode usar HTTP ou HTTPS.

O aplicativo de player ASP.NET usa HTTPS como uma melhor prática, portanto o Player de Mídia é uma página em HTTPS. No entanto, o HTTP é preferencial para streaming, então é necessário considerar estes problemas com conteúdo misto:

* O navegador não permite conteúdo misto. Mas plug-ins como os do Silverlight e do OSMF para Smooth e DASH permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça potencial de injeção de JavaScript mal-intencionado, o que pode colocar os dados do cliente em risco. Os navegadores bloqueiam essa funcionalidade por padrão. A única maneira de contornar isso é no lado do servidor (origem), por meio da permissão de todos os domínios (sejam HTTPS ou HTTP). Isso provavelmente também não será uma boa ideia.
* Evite conteúdo misto. O aplicativo de player e o Player de Mídia devem usar HTTP ou HTTPS. Durante a reprodução de conteúdo misto, a tecnologia SilverlightSS exige a desmarcação de um aviso de conteúdo misto. A tecnologia FlashSS lida com conteúdo misto sem um aviso.
* Se o ponto de extremidade de streaming tiver sido criado antes de agosto de 2014, ele não será compatível com HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E quanto à Transmissão ao Vivo?

Você pode usar exatamente o mesmo design e a mesma implementação para proteger a transmissão ao vivo nos Serviços de Mídia, tratando o ativo associado a um programa como um ativo de VoD. Para proteger o conteúdo ao vivo com vários DRMs, aplique ao ativo a mesma configuração/processamento de um ativo VOD antes de associá-lo à saída ao vivo.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Mídia?

Muitas vezes os clientes investiram em um farm de servidores de licença em um data center próprio ou hospedado por provedores de serviço de DRM. Com a Proteção de Conteúdo dos Serviços de Mídia, você pode operar em modo híbrido. O conteúdo pode ser hospedado e protegido dinamicamente nos Serviços de Mídia, enquanto as licenças de DRM são fornecidas pelos servidores fora dos Serviços de Mídia. Nesse caso, considere as seguintes alterações:

* O STS precisa emitir tokens que sejam aceitáveis e possam ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pela Axinom exigem um JWT específico que contém uma mensagem de autorização. Você precisa ter um STS para emitir esse JWT. 
* Você não precisa mais configurar o serviço de entrega de licença nos Serviços de Mídia. Você precisa fornecer URLs de aquisição de licença (para o PlayReady, o Widevine e o FairPlay) ao configurar o `ContentKeyPolicy`.

> [!NOTE]
> O Widevine é um serviço fornecido pela Google e está sujeito aos termos de serviço e à política de privacidade da Google.

## <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 comparado com v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Eu posso usar o portal do Azure para gerenciar recursos da v3?

Atualmente, você pode usar o [portal do Azure](https://portal.azure.com/) para:

* Gerenciar [eventos ao vivo](live-event-outputs-concept.md) nos Serviços de Mídia v3. 
* Ver (não gerenciar) os [ativos](assets-concept.md) de v3. 
* [Obter informações sobre o acesso a APIs](./access-api-howto.md). 

Para todas as outras tarefas de gerenciamento (por exemplo, [Transformações e Trabalhos](transform-jobs-concept.md) e [Proteção de conteúdo](drm-content-protection-concept.md)), use a [API REST](/rest/api/media/), a [CLI do Azure](/cli/azure/ams) ou um dos [SDKs](media-services-apis-overview.md#sdks) com suporte.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile no v3?

O conceito `AssetFile` foi removido da API dos Serviços de Mídia para separar os Serviços de Mídia das dependências do SDK do Armazenamento. Agora o Armazenamento do Microsoft Azure, não os Serviços de Mídia, mantém as informações que pertencem ao SDK de armazenamento. 

Para obter mais informações, confira [Migrar para os Serviços de Mídia v3](migrate-v-2-v-3-migration-introduction.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Onde está a criptografia de armazenamento do lado do cliente?

Agora recomendamos usar a criptografia de armazenamento do lado do servidor (que está ativada por padrão). Para obter mais informações, confira [Criptografia do Serviço de Armazenamento do Microsoft Azure para dados inativos](../../storage/common/storage-service-encryption.md).

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>Streaming de FairPlay para iOS

As perguntas frequentes a seguir auxiliam na solução de problemas de streaming de FairPlay offline para iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Por que apenas o áudio toca, mas nenhum vídeo aparece durante o modo offline?

Esse comportamento parece estar relacionado ao design do aplicativo de exemplo. Quando uma faixa de áudio alternativa está presente (que é o caso de HLS) durante o modo offline, o iOS 10 e o iOS 11 têm como padrão a faixa de áudio alternativa. Para compensar esse comportamento para o modo offline de FPS, remova a faixa de áudio alternativa do fluxo. Para fazer isso nos Serviços de Mídia, adicione o filtro de manifesto dinâmico **audio-only=false**. Em outras palavras, uma URL de HLS termina com **.ism/manifest(format=m3u8-aapl,audio-only=false)** . 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Por que ela ainda toca áudio somente sem vídeo durante o modo offline após eu adicionar audio-only=false?

Dependendo do design da chave do cache da rede de distribuição de conteúdo, o conteúdo pode ser armazenado em cache. Limpe o cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>O modo de FPS offline também tem suporte no iOS 11 além do iOS 10?

Sim. O modo offline de FPS tem suporte para iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Por que eu não consigo encontrar o documento “Reprodução Offline com o Streaming de FairPlay e HTTP Live Streaming no SDK do Servidor de FPS”?

Desde a versão 4 do SDK do Servidor de FPS, este documento foi mesclado no “Guia de Programação de Streaming de FairPlay”.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>O que é a estrutura de arquivos offline/baixados em dispositivos iOS?

A estrutura do arquivo baixado em um dispositivo iOS tem a aparência da captura de tela abaixo. A pasta `_keys` armazena licenças de FPS baixadas, com um arquivo de armazenamento para cada host de serviço de licença. A pasta `.movpkg` armazena conteúdo de áudio e vídeo. 

A primeira pasta com o nome que termina com um traço seguido por um número contém o conteúdo de vídeo. O valor numérico é a largura de banda de pico das representações de vídeo. A segunda pasta com um nome que termina com um traço seguido por 0 contém o conteúdo de áudio. A terceira pasta denominada `Data` contém a playlist mestre do conteúdo de FPS. Por fim, boot.xml fornece uma descrição completa do conteúdo da pasta `.movpkg`. 

![Estrutura do arquivo offline para o aplicativo iOS de exemplo do FairPlay](media/drm-offline-fairplay-for-ios-concept/offline-fairplay-file-structure.png)

Veja um arquivo de exemplo boot.xml:

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Como fornecer licenças persistentes (habilitadas offline) para alguns clientes/usuários e licenças não persistentes (desabilitadas offline) para outros? É necessário duplicar o conteúdo e usar uma chave de conteúdo separada?

Como os Serviços de Mídia v3 permitem que um ativo tenha várias instâncias do `StreamingLocator`, você pode ter:

* Uma `ContentKeyPolicy` instância com `license_type = "persistent"`, `ContentKeyPolicyRestriction` com declaração sobre `"persistent"` seu `StreamingLocator`.
* Outra `ContentKeyPolicy` instância com `license_type="nonpersistent"`, `ContentKeyPolicyRestriction` com declaração sobre `"nonpersistent`" e seu `StreamingLocator`.
* Duas `StreamingLocator` instâncias que têm valores de `ContentKey` diferentes.

Dependendo da lógica de negócios do STS personalizado, diferentes declarações são emitidas no token JWT. Com o token, apenas a licença correspondente pode ser obtida e apenas a URL correspondente pode ser reproduzida.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual é o mapeamento entre os níveis de segurança do Widevine e do DRM dos Serviços de Mídia?

A "Visão geral da arquitetura de DRM do Widevine", do Google, define três níveis de segurança. No entanto, a [documentação dos Serviços de Mídia do Azure no modelo de licença do Widevine](drm-widevine-license-template-concept.md) descreve cinco níveis de segurança (requisitos de robustez do cliente para reprodução). Esta seção explica o mapeamento dos níveis de segurança.

Ambos os conjuntos de níveis de segurança são definidos pelo Google Widevine. A diferença está no nível de uso: arquitetura ou API. Os cinco níveis de segurança são usados na API do Widevine. O objeto `content_key_specs`, que contém `security_level`, é desserializado e passado para o serviço de entrega global do Widevine pelo serviço de licença do Widevine nos Serviços de Mídia do Azure. A tabela a seguir mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura do Widevine** |**Níveis de segurança usados na API do Widevine**|
|---|---| 
| **Nível de Segurança 1**: todo o processamento, criptografia e controle de conteúdo é realizado no TEE (Ambiente de Execução Confiável). Em alguns modelos de implementação, o processamento da segurança pode ser feito em diferentes chips.|**security_level=5**: a criptografia, a decodificação e qualquer manipulação da mídia (compactada e descompactada) precisam ser feitas em um TEE com suporte de hardware.<br/><br/>**security_level=4**: a criptografia e a decodificação do conteúdo precisam ser feitas em um TEE com suporte de hardware.|
**Nível de segurança 2**: a criptografia (mas não o processamento de vídeo) é executada no TEE. Os buffers descriptografados são retornados para o domínio do aplicativo e processados por meio de um hardware ou software de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas somente no TEE.| **security_level=3**: as operações de criptografia e material de chave precisam ser executadas em um TEE com suporte de hardware. |
| **Nível de segurança 3**: não há TEE no dispositivo. Podem ser tomadas medidas apropriadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de Nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso melhora apenas o desempenho, não a segurança. | **security_level=2**: a criptografia de software e um decodificador oculto são obrigatórios.<br/><br/>**security_level=1**: a criptografia whitebox baseada em software é obrigatória.|

#### <a name="why-does-content-download-take-so-long"></a>Por que o download de conteúdo leva muito tempo?

Há duas maneiras para melhorar a velocidade do download:

* Habilitar uma rede de distribuição de conteúdo, de modo que seja mais provável que os usuários a acessem, em vez da origem e/ou do ponto de extremidade de streaming para o download de conteúdo. Se o usuário acessar o ponto de extremidade de streaming, cada segmento de HLS ou fragmento de DASH será empacotado e criptografado dinamicamente. Embora essa latência esteja na escala de milissegundos para cada segmento ou fragmento, quando você tem um vídeo com duração de uma hora, a latência acumulada pode ser grande e levar a um download mais longo.
* Dê aos usuários finais a opção de baixar seletivamente camadas de qualidade de vídeo e faixas de áudio, em vez de todo o conteúdo. Para o modo offline, não faz sentido baixar todas as camadas de qualidade. Há duas maneiras de fazer isso:

  * Controlado pelo cliente: o aplicativo de player seleciona automaticamente ou o usuário seleciona a camada de qualidade de vídeo e as faixas de áudio a serem baixadas.
  * Controlado pelo serviço: é possível usar o recurso de Manifesto Dinâmico nos Serviços de Mídia do Azure para criar um filtro (global), que limita a playlist de HLS ou o DASH MPD a uma só camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, a URL de download apresentada aos usuários incluirá esse filtro.

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)