---
title: Filtros e manifestos dinâmicos dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: bbbb570cc042d5faa16b66c42aef9792b24fdb12
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854066"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Pré-filtrando manifestos usando o Dynamic Packager

Quando você estiver distribuindo conteúdo de streaming de taxa de bits adaptável para dispositivos, você geralmente precisará publicar várias versões de um manifesto para direcionar recursos de dispositivo específicos ou largura de banda de rede disponível. O [Gerenciador dinâmico](dynamic-packaging-overview.md) permite que você especifique filtros que podem filtrar codecs específicos, resoluções, taxas de bits e combinações de faixas de áudio imediatamente, eliminando a necessidade de criar várias cópias. Você simplesmente precisa publicar uma nova URL com um conjunto específico de filtros configurados para seus dispositivos de destino (iOS, Android, SmartTV ou navegadores) e os recursos de rede (cenários de alta largura de banda, móvel ou baixa largura de banda). Nesse caso, os clientes podem manipular o streaming do seu conteúdo por meio da cadeia de caracteres de consulta (especificando [filtros de ativo disponíveis ou filtros de conta](filters-concept.md)) e usar filtros para transmitir seções específicas de um fluxo.

Alguns cenários de entrega exigem que você verifique se um cliente não consegue acessar faixas específicas. Por exemplo, talvez você não queira publicar um manifesto que contenha faixas de HD em uma camada de assinante específica. Ou, talvez você queira remover faixas do ABR (taxa de bits adaptável) específicas para reduzir o custo de entrega para um dispositivo específico que não se beneficiaria das faixas adicionais. Nesse caso, você pode associar uma lista de filtros criados previamente com o localizador de [streaming](streaming-locators-concept.md) na criação. Nesse caso, os clientes não podem manipular como o conteúdo é transmitido, ele é definido pelo **localizador de streaming**.

Você pode combinar a filtragem por meio da especificação [de filtros no localizador de streaming](filters-concept.md#associating-filters-with-streaming-locator) + filtros específicos de dispositivos adicionais que o cliente especifica na URL. Isso pode ser útil para restringir faixas adicionais, como metadados ou fluxos de eventos, idiomas de áudio ou faixas de áudio descritivas. 

Essa capacidade de especificar filtros diferentes em seu fluxo fornece uma solução de manipulação de **manifesto dinâmico** poderosa para direcionar vários cenários de caso de uso para seus dispositivos de destino. Este tópico explica os conceitos relacionados aos **Manifestos Dinâmicos** e fornece exemplos de cenários nos quais você talvez deseje usar esse recurso.

> [!NOTE]
> Manifestos dinâmicos não alteram o ativo e o manifesto padrão para esse ativo. 
> 

##  <a name="overview-of-manifests"></a>Visão geral dos manifestos

Os serviços de mídia do Azure dão suporte aos protocolos HLS, MPEG DASH e Smooth Streaming. Como parte do [empacotamento dinâmico](dynamic-packaging-overview.md), os manifestos de cliente de streaming (lista de reprodução de mestre HLS, descrição de apresentação de mídia Dash [MPD] e Smooth streaming) são gerados dinamicamente com base no seletor de formato na URL. Consulte os protocolos de entrega em [fluxo de trabalho comum sob demanda](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Obter e examinar arquivos de manifesto

Você especifica uma lista de condições de propriedade de controle de filtro com base nas faixas de seu fluxo (Live ou vídeo por demanda [VOD]) devem ser incluídas em um manifesto criado dinamicamente. Para obter e examinar as propriedades das faixas, você precisa carregar o manifesto Smooth Streaming primeiro.

O tutorial [carregar, codificar e transmitir arquivos com o .net](stream-files-tutorial-with-api.md#get-streaming-urls) mostra como criar as URLs de streaming com o .net. Se você executar o aplicativo, uma das URLs aponta para o manifesto do Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Copie e cole o URL na barra de endereços de um navegador. O arquivo será baixado. Você pode abri-lo em um editor de texto de sua escolha.

Para obter um exemplo de REST, consulte [carregar, codificar e transmitir arquivos com REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorar a taxa de bits de um fluxo de vídeo

Você pode usar a página de demonstração do [ Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para monitorar a taxa de bits de um fluxo de vídeo. A página demonstração exibe informações de diagnóstico na guia **diagnóstico** :

![Diagnóstico do Player de Mídia do Azure][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Exemplos: URLs com filtros na cadeia de caracteres de consulta

Você pode aplicar filtros a protocolos de streaming do ABR: HLS, MPEG-DASH e Smooth Streaming. A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocol|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtragem de representação

Você pode optar por codificar seu ativo para vários perfis de codificação (H. 264 linha de base, H. 264 High, AACL, AACH, Dolby Digital Plus) e várias taxas de bits de qualidade. No entanto, nem todos os dispositivos de cliente oferecerão suporte a todos os seus perfis e taxas de bits de todos os seus ativos. Por exemplo, dispositivos Android mais antigos dão suporte apenas a H. 264 Baseline + AACL. O envio de taxas de bits mais altas para um dispositivo que não pode obter os benefícios desperdiça a largura de banda e a computação de dispositivos. Esse dispositivo deve decodificar todas as informações fornecidas, apenas para diminuí-la para exibição.

Com o manifesto dinâmico, você pode criar perfis de dispositivo (como Mobile, console ou HD/SD) e incluir as trilhas e qualidades que você deseja que sejam parte de cada perfil. Isso é chamado de filtragem de representação. O diagrama a seguir mostra um exemplo dele.

![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, o codificador foi usado para codificar um ativo mezzanine em sete representações de vídeo ISO MP4s (de 180p para 1080p). O ativo codificado pode ser [empacotado dinamicamente](dynamic-packaging-overview.md) em qualquer um dos seguintes protocolos de streaming: HLS, MPEG DASH e Smooth. 

A parte superior do diagrama a seguir mostra o manifesto HLS para o ativo sem filtros. (Ele contém todas as sete representações.)  No canto inferior esquerdo, o diagrama mostra um manifesto HLS ao qual um filtro chamado "Ott" foi aplicado. O filtro "Ott" especifica a remoção de todas as taxas de bits abaixo de 1 Mbps, portanto os dois níveis de qualidade inferiores foram eliminados na resposta. No canto inferior direito, o diagrama mostra o manifesto HLS ao qual um filtro chamado "Mobile" foi aplicado. O filtro "móvel" especifica a remoção de representações em que a resolução é maior que 720p, portanto, as duas rendições de 1080p foram eliminadas.

![Filtragem de representação][renditions1]

## <a name="removing-language-tracks"></a>Remover faixas de idiomas
Os ativos podem incluir vários idiomas de áudio, como inglês, espanhol, francês etc. Normalmente, o SDK do Player gerencia a seleção da faixa de áudio padrão e as faixas de áudio disponível por seleção do usuário.

Desenvolver esses SDKs de Player é desafiador, pois requer implementações diferentes nas estruturas de Player específicas do dispositivo. Além disso, em algumas plataformas, as APIs do Player são limitadas e não incluem o recurso de seleção de áudio, em que os usuários não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, é possível controlar o comportamento por meio da criação de filtros que incluem apenas os idiomas de áudio desejados.

![Filtragem de faixas de idioma][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Corte do início de um ativo

Na maioria dos eventos de transmissão ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, podem incluir um slate antes do início do evento com a seguinte frase: "O programa será iniciado momentaneamente". 

Se o programa estiver sendo arquivado, o teste e os dados da imagem fixa também são arquivados e incluídos na apresentação. No entanto, essas informações não devem ser mostradas para os clientes. Com o manifesto dinâmico, é possível criar um filtro de hora de início e remover os dados indesejados do manifesto.

![Corte do início][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criar subclipes (exibições) de um arquivo ao vivo

Muitos eventos ao vivo são de longa duração e o arquivamento dinâmico pode incluir vários eventos. Após o término do evento ao vivo, talvez os difusores queiram dividir o arquivo ao vivo em sequências lógicas de início e parada do programa. 

Você pode publicar esses programas virtuais separadamente sem o pós-processamento do arquivo em tempo real e não a criação de ativos separados (o que não obtém o benefício dos fragmentos armazenados em cache existentes no CDNs). Exemplos desses programas virtuais são os tempos de um jogo de futebol ou de basquete, entradas no beisebol ou eventos individuais de qualquer programa de esportes.

Com o manifesto dinâmico, você pode criar filtros usando horários de início/término e criar exibições virtuais na parte superior do seu arquivo em tempo real. 

![Filtro de subclipe][subclip_filter]

Este é o ativo filtrado:

![Esqui][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Ajustando a janela de apresentação (DVR)

Atualmente, os serviços de mídia do Azure oferecem um arquivo circular em que a duração pode ser configurada entre 5 minutos e 25 horas. A filtragem de manifesto pode ser usada para criar uma janela DVR com rolagem que ultrapassa o arquivo, sem excluir a mídia. Há muitos cenários em que os difusores podem desejar fornecer uma janela DVR limitada que se move com a borda ao vivo e, ao mesmo tempo manter uma janela de arquivamento maior. Um transmissor pode querer usar os dados que estão fora da janela do DVR para destacar os clipes, ou eles podem querer fornecer diferentes janelas do DVR para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lida com janelas DVR grandes (você pode ter uma janela DVR de 2 minutos para dispositivos móveis e uma hora para clientes de desktop).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar o LiveBackoff (posição ao vivo)

A filtragem de manifesto pode ser usada para remover vários segundos da borda ao vivo de um programa ao vivo. A filtragem permite que os difusores assistam à apresentação no ponto de publicação de visualização e criem pontos de inserção de anúncio antes que os visualizadores recebam o fluxo (apoiado em 30 segundos). Os difusores podem enviar esses anúncios para suas estruturas de cliente no tempo para que eles recebam e processem as informações antes da oportunidade de anúncio.

Além do suporte a anúncios, a configuração de retirada ao vivo pode ser usada para ajustar a posição dos visualizadores para que, quando os clientes se descompassom e atinjam o Live Edge, ainda possam obter fragmentos do servidor. Dessa forma, os clientes não receberão um erro HTTP 404 ou 412.

![Filtrar para retirada ao vivo][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinar várias regras em um único filtro

É possível combinar várias regras de filtragem em um único filtro. Por exemplo, você pode definir uma “regra de intervalo” para remover a imagem fixa de um arquivo ao vivo e filtrar as taxas de bits disponíveis. Quando você estiver aplicando várias regras de filtragem, o resultado final será a interseção de todas as regras.

![Várias regras de filtragem][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinando vários filtros (composição de filtros)

Você também pode combinar vários filtros em uma única URL. O seguinte cenário demonstra por que talvez seja conveniente combinar filtros:

1. Você precisa filtrar suas qualidades de vídeo para dispositivos móveis, como Android ou iPad (para limitar as qualidades de vídeo). Para remover as qualidades indesejadas, você criará um filtro de conta adequado para os perfis de dispositivo. Você pode usar filtros de conta para todos os seus ativos na mesma conta de serviços de mídia sem nenhuma associação adicional.
1. Você também deseja cortar a hora de início e de término de um ativo. Para conseguir isso, você criará um filtro de ativo e definirá a hora de início/término. 
1. Você deseja combinar esses dois filtros. Sem a combinação, você precisaria adicionar a filtragem de qualidade ao filtro de corte, o que dificultaria o uso do filtro.


Para combinar filtros, você precisa definir os nomes de filtro para a URL de manifesto/playlist no formato delimitado por ponto e vírgula. Vamos supor que você tenha um filtro chamado *MyMobileDevice* que filtre qualidades, e que você tenha outro  chamado mystartime para definir uma hora de início específica. Você pode combinar até três filtros. 

Para saber mais, confira [esta postagem no blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Considerações e limitações

- Os valores de **forceEndTimestamp**, **presentationWindowDuration**e **liveBackoffDuration** não devem ser definidos para um filtro VOD. Eles são usados somente para cenários de filtros em tempo real. 
-  Um manifesto dinâmico opera em limites de GOP (quadros-chave), portanto, o corte tem precisão de GOP.
-  Você pode usar o mesmo nome de filtro para filtros de conta e de ativo. Os filtros de ativos têm maior precedência e substituirão os filtros de conta.
- Se você atualizar um filtro, pode levar até 2 minutos para que o ponto de extremidade de streaming atualize as regras. Se você usou filtros para fornecer o conteúdo (e você armazenou em cache o conteúdo em proxies e caches CDN), a atualização desses filtros pode resultar em falhas do Player. Recomendamos que você limpe o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.
- Os clientes precisam baixar manualmente o manifesto e analisar o carimbo de data/hora de início exato e a escala de tempo.
    
    - Para determinar as propriedades das faixas em um ativo, [obtenha e examine o arquivo de manifesto](#get-and-examine-manifest-files).
    - A fórmula para definir as propriedades de carimbo de data/hora do filtro de ativos é: <br/>startTimestamp = &lt;hora de início no manifesto&gt; +  &lt;tempo de início de filtro esperado&gt; em segundos * escala de tempo

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram como criar filtros programaticamente:  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
