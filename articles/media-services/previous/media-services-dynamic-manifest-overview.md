---
title: Filtros e manifestos dinâmicos | Microsoft Docs
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para arquivar esse streaming seletivo.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: 229f89a8803f089c24981f56e00e36efe96de3aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465067"
---
# <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versão 2](media-services-dynamic-manifest-overview.md)
> * [Versão 3](../latest/filters-dynamic-manifest-overview.md)

A partir da versão 2.17, os Serviços de Mídia do Microsoft Azure permitem definir filtros para seus ativos. Esses filtros são regras do lado do servidor que permitirão aos clientes optar por realizar ações como: reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro) ou especificar apenas um subconjunto de representações de áudio e vídeo com o qual o dispositivo do cliente pode lidar (em vez de todas as representações que estão associadas ao ativo). A filtragem de ativos é obtida por meio de **Manifestos Dinâmicos**criados mediante solicitação do cliente para transmitir um vídeo com base em filtros especificados.

Este tópico analisa cenários comuns nos quais o uso dos filtros será muito útil para seus clientes e links para tópicos que demonstram como criar filtros de forma programática.

## <a name="overview"></a>Visão geral
Ao fornecer conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda) sua meta é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para atingir essa meta, faça o seguinte:

* codifique seu fluxo para múltiplas taxas de bits ([taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) transmissão de vídeo (isso também tratará das condições de rede e de qualidade) e 
* use o [Empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos serviços de mídia para reempacotar dinamicamente seu fluxo em protocolos diferentes (isso se encarregará da transmissão em dispositivos diferentes). Os Serviços de Mídia permitem a entrega das seguintes tecnologias de streaming de taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH. 

### <a name="manifest-files"></a>Arquivos de manifesto
Ao codificar um ativo para streaming de taxa de bits adaptável, um arquivo de **manifesto** (reprodução) é criado (o arquivo é baseado em texto ou XML). O arquivo de **manifesto** inclui o streaming de metadados, como: tipo da trilha (áudio, vídeo ou texto), nome da trilha, hora de início e término, taxa de bits (qualidades), idiomas da trilha, janela de apresentação (janela deslizante de duração fixa), codec de vídeo (FourCC). Também instrui o player a recuperar o próximo fragmento, fornecendo informações sobre os próximos fragmentos de vídeo executáveis disponíveis e sua localização. Os fragmentos (ou segmentos) são as "partes" reais de um conteúdo de vídeo.

Aqui está um exemplo desse arquivo de manifesto: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Manifestos dinâmicos
Há [cenários](media-services-dynamic-manifest-overview.md#scenarios) em que o cliente precisa de mais flexibilidade do que o que é descrito no arquivo de manifesto do ativo padrão. Por exemplo: 

* Dispositivo específico: entregar apenas as representações especificadas e/ou faixas de idioma especificadas com suporte pelo dispositivo que é usado para reproduzir o conteúdo ("filtragem da representação"). 
* Redução do manifesto para mostrar um subclipe de um evento ao vivo ("filtragem de subclipe").
* Corte do início de um vídeo ("corte de um vídeo").
* Ajuste a Janela de Apresentação (DVR) para fornecer uma duração limitada da janela do DVR no leitor ("ajustar a janela de apresentação").

Para atingir esta flexibilidade, os serviços de mídia oferecem os **manifestos dinâmico** com base em [filtros](media-services-dynamic-manifest-overview.md#filters)predefinidos.  Depois de definir os filtros, os clientes podem usá-los para transmitir uma representação específica ou subclipes do vídeo. Eles podem especificar filtros na URL de transmissão. Os filtros podem ser aplicados nos protocolos de streaming de taxa de bits adaptável com suporte do [Empacotamento Dinâmico](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH e Smooth Streaming. Por exemplo: 

URL de MPEG DASH com filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de Smooth Streaming com filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para obter mais informações sobre como fornecer seu conteúdo e criar URLs de streaming, consulte [Visão geral do fornecimento de conteúdo](media-services-deliver-content-overview.md).

> [!NOTE]
> Observe que os Manifestos dinâmicos não alteram o ativo e nem o manifesto padrão para esse ativo. O cliente pode optar por solicitar um fluxo com ou sem filtros. 
> 
> 

### <a id="filters"></a>Filtros
Há dois tipos de filtros de ativo: 

* Filtros globais (podem ser aplicados a qualquer ativo na conta de Serviços de Mídia do Azure, têm a vida útil da conta) e 
* Filtros locais (podem ser aplicados somente a um ativo com a qual o filtro foi associado no momento da criação, têm a vida útil do ativo). 

Os filtros globais e locais têm exatamente as mesmas propriedades. A principal diferença entre os dois é para quais cenários, que tipo de filtro é mais adequado. Os filtros globais geralmente são adequados para perfis de dispositivos (filtragem de representação) em que os filtros locais poderiam ser usados para cortar um ativo específico.

## <a id="scenarios"></a>Cenários comuns
Como foi mencionado anteriormente, ao fornecer conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda), sua meta é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Além disso, você pode ter outros requisitos que envolvem a filtragem dos ativos e uso de **Manifestos Dinâmicos**. As seções a seguir proporcionam uma breve visão geral dos diferentes cenários de filtragem.

* Especifique apenas um subconjunto das representações de áudio e vídeos que podem tratar certos dispositivos (em vez de todas as representações que estão associadas ao ativo). 
* Reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro).
* Ajuste da janela de apresentação de DVR.

## <a name="rendition-filtering"></a>Filtragem de representação
Você pode optar por codificar seu ativo para vários perfis de codificação (H.264 linha de base, H.264 alto, AACL, AACH, Dolby Digital Plus) e várias taxas de bits de qualidade. No entanto, nem todos os dispositivos de cliente oferecerão suporte a todos os seus perfis e taxas de bits de todos os seus ativos. Por exemplo, os dispositivos Android mais antigos oferecem suporte apenas a H.264 Baseline+AACL. Enviar taxas de bits mais altos para um dispositivo que não consegue aproveitar os benefícios, representa um desperdício de largura de banda e do dispositivo de computação. Esse dispositivo deve decodificar todas as informações determinadas, apenas para que seja subdimensionado para exibição.

Com o manifesto dinâmico, é possível criar perfis de dispositivos, como dispositivos móveis, console, HD/SD, etc., além de incluir as trilhas e qualidades que você deseja que faça parte de cada perfil.

![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, o codificador foi usado para codificar um ativo mezzanine em sete representações de vídeo ISO MP4s (de 180p para 1080p). O recurso codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de streaming: HLS, Smooth e MPEG DASH.  Na parte superior do diagrama, é mostrado o manifesto HLS para o ativo sem filtros (ele contém todas as sete representações).  Na parte inferior esquerda, é mostrado o manifesto HLS ao qual foi aplicado um filtro chamado "ott". O filtro de "ott" especifica a remoção de todas as taxas de bits abaixo de 1Mbps, resultando na remoção dos dois níveis de qualidade inferiores da resposta. Na parte inferior direita, é mostrado o manifesto HLS, ao qual foi aplicado um filtro chamado "mobile". O filtro "mobile" especifica a remoção de representações em que a resolução é maior do que 720p, resultando na remoção de duas representações de 1080p.

![Filtragem de representação][renditions1]

## <a name="removing-language-tracks"></a>Remover faixas de idiomas
Os ativos podem incluir vários idiomas de áudio, como inglês, espanhol, francês etc. Normalmente, o SDK do Player gerencia a seleção da faixa de áudio padrão e as faixas de áudio disponível por seleção do usuário. O desenvolvimento desses SDKs do Player é um desafio, requer diferentes implementações em estruturas de player específicas do dispositivo. Além disso, em algumas plataformas, os APIs de Player são limitados e não incluem o recurso de seleção de áudio em que os usuários não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, é possível controlar o comportamento por meio da criação de filtros que incluem apenas os idiomas de áudio desejados.

![Filtragem das faixas de idioma][language_filter]

## <a name="trimming-start-of-an-asset"></a>Corte do início de um ativo
Na maioria dos eventos de transmissão ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, podem incluir um slate antes do início do evento com a seguinte frase: "O programa será iniciado em instantes". Se o programa estiver sendo arquivado, o teste e os dados da imagem fixa também são arquivados e incluídos na apresentação. No entanto, essas informações não devem ser mostradas para os clientes. Com o manifesto dinâmico, é possível criar um filtro de hora de início e remover os dados indesejados do manifesto.

![Corte do início][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criar subclipes (exibições) de um arquivo ao vivo
Muitos eventos ao vivo são de longa duração e o arquivamento dinâmico pode incluir vários eventos. Após o término do evento ao vivo, talvez os difusores queiram dividir o arquivo ao vivo em sequências lógicas de início e parada do programa. Em seguida, publicam esses programas virtuais separadamente sem pós-processamento do arquivo em tempo real e sem criar ativos separados (o que não obtém os benefícios dos fragmentos de cache existentes nas CDNs). Exemplos desses programas virtuais são os tempos de um jogo de futebol ou de basquete, entradas no beisebol ou eventos individuais de qualquer programa de esportes.

Com o manifesto dinâmico, é possível criar filtros usando os horários de início/término e criar modos de exibição virtuais que ultrapassam seu arquivo ao vivo. 

![Filtro de subclipe][subclip_filter]

Ativos filtrados:

![Esqui][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Ajuste da janela de apresentação (DVR)
Atualmente, o Serviços de Mídia do Azure oferece arquivamento circular onde a duração pode ser configurada entre 5 minutos e 25 horas. A filtragem de manifesto pode ser usada para criar uma janela DVR com rolagem que ultrapassa o arquivo, sem excluir a mídia. Há muitos cenários em que os difusores podem desejar fornecer uma janela DVR limitada que se move com a borda ao vivo e, ao mesmo tempo manter uma janela de arquivamento maior. Talvez o difusor queira usar os dados que estão fora da janela DVR para realçar clipes ou talvez ele queira fornecer janelas DVR diferentes para dispositivos diferentes. Por exemplo, a maioria dos dispositivos móveis não lida com janelas DVR grandes (você pode ter uma janela DVR de 2 minutos para dispositivos móveis e de uma hora para os clientes de desktop).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar o LiveBackoff (posição ao vivo)
A filtragem de manifesto pode ser usada para remover vários segundos da borda ao vivo de um programa ao vivo. A filtragem permite que os difusores assistam à apresentação no ponto de publicação de visualização e criem pontos de inserção de anúncio antes que os visualizadores recebam o fluxo (retirado por 30 segundos). Os difusores, enviam esses anúncios para suas estruturas de cliente no horário para que eles recebam e processem as informações antes da oportunidade de anúncio.

Além do suporte do anúncio, o a configuração do LiveBackoff pode ser usada para ajustar a posição dos visualizadores para que, quando os clientes se deslocarem e alcançarem a borda ao vivo, ainda possam obter fragmentos do servidor em vez de obter um erro HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinar várias regras em um único filtro
É possível combinar várias regras de filtragem em um único filtro. Por exemplo, você pode definir uma “regra de intervalo” para remover a imagem fixa de um arquivo ao vivo e filtrar as taxas de bits disponíveis. Ao aplicar várias regras de filtragem, o resultado final é a intersecção de todas essas regras.

![várias regras][multiple-rules]

## <a name="create-filters-programmatically"></a>Criar filtros por meio de programa
O artigo a seguir analisa as entidades dos Serviços de Mídia do Azure relacionadas aos filtros. O artigo também mostra como criar filtros programaticamente.  

[Criar filtros com APIs REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinando vários filtros (composição de filtros)
Você também pode combinar vários filtros em uma única URL. 

O seguinte cenário demonstra por que talvez seja conveniente combinar filtros:

1. Você precisa filtrar as qualidades de seus vídeos para dispositivos móveis, como Android ou iPAD (para limitar as qualidades de vídeos). Para remover as qualidades indesejadas, você criaria um filtro global adequado para os perfis de dispositivos. Como mencionado anteriormente neste artigo, os filtros globais podem ser usados para todos os seus ativos com a mesma conta de serviços de mídia sem qualquer outra associação. 
2. Você também deseja cortar a hora de início e de término de um ativo. Para conseguir isso, você criaria um filtro local e definiria a hora de início/término. 
3. Você deseja combinar esses dois filtros (sem a combinação, você precisa adicionar a filtragem de qualidade ao filtro de corte, o que dificultará ainda mais o uso do filtro).

Para combinar os filtros, você precisa definir os nomes dos filtros para a URL do manifesto/playlist com ponto e vírgula delimitado. Vamos supor que você tenha um filtro chamado *MyMobileDevice* que filtra as qualidades e tenha outro chamado *MyStartTime* para definir uma hora de início específica. Você pode combiná-los assim:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Você pode combinar até três filtros. 

Para saber mais, confira [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Conheça os problemas e limitações
* Manifesto dinâmico opera nos limites do GOP (quadros chave) e, como consequência, o corte tem precisão de GOP. 
* É possível usar o mesmo nome de filtro para os filtros globais e locais. Os filtros locais têm maior precedência e substituem os filtros globais.
* Ao atualizar um filtro, talvez sejam necessários até 2 minutos para que o ponto de extremidade do streaming atualize as regras. Se o conteúdo foi distribuído usando alguns filtros (e armazenado em cache nos proxies e caches CDN), atualizar esses filtros pode resultar em falhas do player. É recomendável limpar o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Visão geral do fornecimento de conteúdo a clientes](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
