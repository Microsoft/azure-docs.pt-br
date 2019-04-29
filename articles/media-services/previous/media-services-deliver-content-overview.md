---
title: Entrega de conteúdo aos clientes | Microsoft Docs
description: Este tópico fornece uma visão geral do que está envolvido no fornecimento de conteúdo com os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5db2cb983c0c3cd0e2194f7686964d9ec3828d6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232261"
---
# <a name="deliver-content-to-customers"></a>Distribuir conteúdo aos clientes
Quando você estiver distribuindo conteúdo de streaming ou vídeo sob demanda aos clientes, sua meta será distribuir vídeo de alta qualidade a vários dispositivos sob diferentes condições de rede.

Para atingir esse objetivo, você pode:

* Codificar o fluxo para uma transmissão de vídeo de múltiplas taxas de bits (taxa de bit adaptável). Isso cuidará da qualidade e das condições de rede.
* Usar o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Serviços de Mídia do Microsoft Azure para reempacotar dinamicamente a transmissão em diferentes protocolos. Isso se encarregará da transmissão em diferentes dispositivos. Os Serviços de Mídia permitem a entrega das seguintes tecnologias de streaming de taxa de bits adaptável: <br/>
    * **HLS** (HTTP Live Streaming): adicione o caminho "(format = m3u8-aapl)" à parte "/Manifest" da URL para informar o servidor de origem de streaming para retornar o conteúdo HLS para consumo nos dispositivos nativos **Apple iOS** (para saber mais detalhes, consulte [localizadores](#locators) e [URLs](#URLs)),
    * **MPEG-DASH**: adicione o caminho "(format=mpd-time-csf)" à parte "/Manifest" da URL para informar o servidor de origem de streaming para retornar MPEG-DASH (para saber mais detalhes, consulte [localizadores](#locators) e [URLs](#URLs)),
    * **Smooth Streaming**.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 

Este artigo apresenta uma visão geral de conceitos importantes de distribuição de conteúdo.

Para verificar os problemas conhecidos, confira [Problemas conhecidos](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Empacotamento dinâmico
Com o empacotamento dinâmico que os Serviços de Mídia fornecem, você pode distribuir seu conteúdo codificado por Smooth Streaming ou MP4 de taxa de bits adaptável em formatos de transmissão com suporte dos Serviços de Mídia (MPEG-DASH, HLS, Smooth Streaming) sem precisar empacotar novamente nesses formatos de streaming. É recomendável distribuir conteúdo com empacotamento dinâmico.

Para aproveitar o empacotamento dinâmico, você precisa codificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável.

Com o empacotamento dinâmico, você armazena e paga por arquivos em um único formato de armazenamento. Os Serviços de Mídia vão criar e fornecer a resposta apropriada com base em suas solicitações.

O empacotamento dinâmico está disponível para pontos de extremidade de streaming Standard e Premium. 

Para saber mais, confira [Empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos
Você pode definir filtros para seus ativos com os Serviços de Mídia. Esses filtros são regras do servidor que ajudam os clientes a realizar tarefas como reproduzir uma seção específica de um vídeo ou especificar um subconjunto de representações de áudio e vídeo com as quais o dispositivo do cliente pode lidar (em vez de todas as representações que estão associadas ao ativo). Essa filtragem é obtida por meio de *manifestos dinâmicos* que são criados quando o cliente solicita transmitir um vídeo com base em um ou mais filtros especificados.

Para obter mais informações, consulte [Filtros e manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Localizadores
Para fornecer ao usuário uma URL que possa ser usada para transmitir ou baixar seu conteúdo, primeiramente você precisa publicar o ativo criando um localizador. Um localizador fornece um ponto de entrada para acessar os arquivos contidos em um ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores:

* Localizadores OnDemandOrigin. Esses localizadores são usados para transmitir mídia (por exemplo, MPEG-DASH, HLS ou Smooth Streaming) ou baixar arquivos progressivamente.
* Localizadores de URL por SAS (Assinatura de Acesso Compartilhado). Esses localizadores são usados para baixar arquivos de mídia para seu computador local.

Uma *política de acesso* é usada para definir as permissões (como leitura, gravação e lista) e por quanto tempo um cliente tem acesso a um ativo específico. Observe que a permissão de lista (AccessPermissions.List) não deve ser usada na criação de um localizador OnDemandOrigin.

Os localizadores têm datas de validade. O portal do Azure define uma data de validade de 100 anos para localizadores.

> [!NOTE]
> Se você usou o portal do Azure para criar localizadores antes de março de 2015, esses localizadores foram definidos para expirar depois de dois anos.
> 
> 

Para atualizar uma data de validade em um localizador, use as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Observe que, quando você atualiza a data de validade de um localizador SAS, a URL é alterada.

Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Você pode conceder diferentes direitos de acesso a usuários individuais usando as soluções DRM (Gerenciamento de Direitos Digitais). Para obter mais informações, consulte [Protegendo mídia](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando você cria um localizador, pode haver um atraso de 30 segundos devido a processos de armazenamento e propagação necessários no Armazenamento do Azure.

## <a name="adaptive-streaming"></a>Streaming adaptável
Tecnologias de taxa de bits adaptável permitem que os aplicativos de player de vídeo determinem as condições da rede e selecionem entre várias taxas de bits. Quando a comunicação da rede é degradada, o cliente pode escolher uma taxa de bits inferior, de modo que a reprodução possa continuar com a qualidade de vídeo inferior. À medida que as condições da rede melhoram, o cliente pode alternar para uma taxa de bits mais alta com qualidade de vídeo aprimorada. Os Serviços de Mídia do Azure dão suporte às seguintes tecnologias de taxa de bits adaptável: HLS (HTTP Live Streaming), Smooth Streaming e MPEG-DASH.

Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. A criação do localizador proporciona o caminho base para o ativo que inclui o conteúdo que você deseja transmitir. No entanto, para poder transmitir esse conteúdo, você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto de streaming, é preciso concatenar o valor do caminho do localizador e o nome de arquivo de manifesto (filename.ism). Em seguida, acrescente **/Manifest** e um formato apropriado (se necessário) ao caminho do localizador.

> [!NOTE]
> Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, certifique-se de que suas URLs de streaming começam com HTTPS. Observe que, atualmente, o AMS não dá suporte ao SSL com domínios personalizados.  
> 

Você só poderá transmitir por SSL se o ponto de extremidade de streaming do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se as URLs de streaming se basearem nos pontos de extremidade de streaming criados após 10 de setembro de 2014, a URL conterá "streaming.mediaservices.windows.net". URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não dão suporte a SSL. Se sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Use as URLs baseadas no novo ponto de extremidade de streaming para transmitir conteúdo por SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Formatos de URL de streaming

### <a name="mpeg-dash-format"></a>Formato MPEG-DASH
{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato Apple HTTP Live Streaming (HLS) V4
{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato Apple HTTP Live Streaming (HLS) V3
{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato HLS (Apple HTTP Live Streaming) com filtro somente áudio
Por padrão, faixas somente áudio são incluídas no manifesto do HLS. Isso é necessário na certificação da Apple Store para redes de celular. Nesse caso, se um cliente não tiver largura de banda suficiente ou estiver conectado por uma conexão 2G, a reprodução será alternada para somente áudio. Isso ajuda a manter o streaming do conteúdo sem buffer, mas sem vídeo. Em alguns cenários, o buffer do player pode ser preferível a somente áudio. Se desejar remover a faixa somente áudio, adicione **audio-only=false** à URL.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Para saber mais, confira [Dynamic Manifest Composition support and HLS output additional features (Suporte à Composição de Manifesto Dinâmico e recursos adicionais da saída de HLS)](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formato Smooth Streaming
{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest

Exemplo:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Manifesto do Smooth Streaming 2.0 (manifesto herdado)
Por padrão, o formato de manifesto do Smooth Streaming contém a marca de repetição (r-tag). No entanto, alguns jogadores não dão suporte à r-tag. Os clientes com esses players podem usar um formato que desabilita a r-tag:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arquivo}.ism/Manifest(format=fmp4-v20)

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Download progressivo
Com o download progressivo, é possível iniciar a reprodução da mídia antes de o arquivo inteiro ter sido baixado. Você não pode baixar progressivamente arquivos .ism* (ismv, isma, ismt ou ismc).

Para baixar conteúdo progressivamente, use o tipo de localizador OnDemandOrigin. O exemplo a seguir mostra a URL que é baseada no tipo de localizador OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

É necessário descriptografar qualquer ativo criptografado por armazenamento que você queira transmitir do serviço de origem para download progressivo.

## <a name="download"></a>Baixar
Para baixar o conteúdo em um dispositivo de cliente, você deve criar um localizador SAS. O localizador SAS dá acesso ao contêiner do Armazenamento do Azure em que o arquivo está localizado. Para criar a URL de download, você deve inserir o nome do arquivo entre o host e a assinatura SAS.

O seguinte exemplo mostra a URL que se baseia no localizador SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

As seguintes considerações se aplicam:

* É necessário descriptografar qualquer ativo criptografado por armazenamento que você queira transmitir do serviço de origem para download progressivo.
* Um download que não foi concluído em 12 horas, falhará.

## <a name="streaming-endpoints"></a>Pontos de extremidade de streaming

Um ponto de extremidade de streaming representa um serviço de streaming que pode distribuir conteúdo diretamente a um aplicativo player do cliente ou a uma CDN (Rede de Distribuição de Conteúdo) para distribuição posterior. O fluxo de saída de um serviço de ponto de extremidade de streaming pode ser uma transmissão ao vivo ou um ativo de vídeo sob demanda em sua conta dos Serviços de Mídia. Há dois tipos de ponto de extremidade de streaming, **Standard** e **Premium**. Para saber mais, confira [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Visão geral dos pontos de extremidade de streaming).

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**. 

## <a name="known-issues"></a>Problemas conhecidos
### <a name="changes-to-smooth-streaming-manifest-version"></a>Alterações na versão do manifesto do Smooth Streaming
Antes da liberação do serviço em julho de 2016 — quando ativos produzidos pelo Codificador de Mídia Padrão, Fluxo de Trabalho Premium de Codificação de Mídia ou o antigo Codificador de Mídia do Azure eram transmitidos usando o empacotamento dinâmico — o manifesto Smooth Streaming retornado seria conforme à versão 2.0. Na versão 2.0, as durações de fragmento não usam as chamadas marcações de repetição ('r'). Por exemplo: 


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Na liberação do serviço de julho de 2016, o manifesto do Smooth Streaming gerado está em conformidade com a versão 2.2, com durações de fragmentos que usam marcações de repetição. Por exemplo: 

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alguns dos clientes herdados do Smooth Streaming podem não dar suporte às marcações de repetição e falharão durante o carregamento do manifesto. Para atenuar esse problema, você pode usar o parâmetro de formato do manifesto herdado **(format=fmp4-v20)** ou atualizar o cliente para a versão mais recente, que dá suporte a marcações de repetição. Para obter mais informações, confira [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Atualizar localizadores dos Serviços de Mídia depois de implantar chaves de armazenamento](media-services-roll-storage-access-keys.md)

