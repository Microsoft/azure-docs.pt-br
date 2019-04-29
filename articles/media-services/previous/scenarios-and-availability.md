---
title: Cenários dos Serviços de Mídia do Microsoft Azure e disponibilidade de recursos nos datacenters | Microsoft Docs
description: Este tópico fornece uma visão geral dos cenários dos Serviços de Mídia do Microsoft Azure e a disponibilidade de recursos e serviços nos datacenters.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3576a50cd1510a4da562981ff9ae98e12a357fc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638161"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Cenários e disponibilidade de recursos dos Serviços de Mídia em datacenters

Os Serviços de Mídia do Microsoft Azure (AMS) permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para a entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

O AMS opera em vários datacenters no mundo inteiro. Esses datacenters estão agrupados em regiões geográficas, oferecendo a você a flexibilidade de escolher onde compilar seus aplicativos. Você pode ver a [lista de regiões e suas localizações](https://azure.microsoft.com/regions/). 

Este tópico mostra os cenários comuns de entrega de conteúdo [ao vivo](#live_scenarios) ou sob demanda. O tópico também fornece detalhes sobre a disponibilidade dos recursos de mídia e serviços nos datacenters.

## <a name="overview"></a>Visão geral

### <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Azure Media Services, você deve possuir o seguinte:

* Uma conta do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Serviços de Mídia do Azure. Para obter mais informações, veja [Criar conta](media-services-portal-create-account.md).
* O ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **Executando**.

    Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta no estado **Parado**. Para começar a transmitir seu conteúdo e aproveitar o empacotamento e a criptografia dinâmicos, o ponto de extremidade de streaming deve estar no estado **Em execução**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Os objetos normalmente usados durante o desenvolvimento no modelo AMS OData

A imagem a seguir mostra alguns dos objetos mais usados ao desenvolver em relação ao modelo de OData de Serviços de Mídia.

Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

Você pode exibir todo o modelo [aqui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteja o conteúdo no armazenamento e forneça mídia de streaming sem proteção (não criptografada)

![Fluxo de trabalho VoD](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Carregue um arquivo de mídia de alta qualidade em um ativo.

    É recomendável aplicar a opção de criptografia de armazenamento a seu ativo para proteger o conteúdo durante o carregamento e enquanto ele estiver em repouso no armazenamento.
2. Codifique para um conjunto de arquivos MP4 com taxa de bits adaptável.

    É recomendável aplicar a opção de criptografia de armazenamento ao ativo de saída para proteger o conteúdo em repouso.
3. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico).

    Se seu ativo tiver o armazenamento criptografado, você **deverá** configurar a política de entrega de ativos.
4. Publicar o ativo criando um localizador OnDemand.
5. Fluxo de conteúdo publicado.

Para obter informações sobre a disponibilidade nos datacenters, consulte a seção [Disponibilidade](#availability).

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger o conteúdo no armazenamento, fornecer mídia de streaming criptografada dinamicamente

![Proteger com o PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Carregue um arquivo de mídia de alta qualidade em um ativo. Aplique a opção de criptografia de armazenamento ao ativo.
2. Codifique para um conjunto de arquivos MP4 com taxa de bits adaptável. Aplique a opção de criptografia de armazenamento ao ativo de saída.
3. Crie uma chave de conteúdo de criptografia para o ativo que você quer que seja criptografado dinamicamente durante a reprodução.
4. Configure a política de autorização de chave de conteúdo.
5. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico e criptografia dinâmica).
6. Publicar o ativo criando um localizador OnDemand.
7. Fluxo de conteúdo publicado.

Para obter informações sobre a disponibilidade nos datacenters, consulte a seção [Disponibilidade](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Use a Análise de Mídia para obter informações acionáveis de seus vídeos

A Análise de Mídia é uma coleção de componentes de fala e de visão que facilitam a obtenção de análises acionáveis dos arquivos de vídeo de organizações e de empresas. Para saber mais, confira [Visão geral a Análise dos Serviços de Mídia do Azure](media-services-analytics-overview.md).

1. Carregue um arquivo de mídia de alta qualidade em um ativo.
2. Processe seus vídeos com um dos serviços de Análise de Mídia descritos na seção [Visão geral da Análise de Mídia](media-services-analytics-overview.md).
3. O processador de mídia da Análise de Mídia produz arquivos MP4 ou arquivos JSON. Se um processador de mídia produzir um arquivo MP4, você poderá baixar o arquivo progressivamente. Se um processador de mídia produzir um arquivo JSON, você poderá baixar o arquivo do Armazenamento de Blobs do Azure.

Para obter informações sobre a disponibilidade nos datacenters, consulte a seção [Disponibilidade](#availability).

## <a name="deliver-progressive-download"></a>Entregar o download progressivo

1. Carregue um arquivo de mídia de alta qualidade em um ativo.
2. Codificar em um único arquivo MP4.
3. Publicar o ativo criando um localizador OnDemand ou SAS.

    Se você estiver usando o localizador de SAS, o conteúdo será baixado do armazenamento de blobs do Azure. Nesse caso, não é necessário ter pontos de extremidade de streaming em estado iniciado.
4. Download progressivo de conteúdo.

## <a id="live_scenarios"></a>Entregando eventos de streaming ao vivo 

1. Inclua o conteúdo ao vivo usando diversos protocolos de streaming ao vivo (por exemplo, RTMP ou Smooth Streaming).
2. (opcionalmente) Codifique seu stream no fluxo de bits adaptável.
3. Visualize seu stream ao vivo.
4. Entregue o conteúdo por meio de protocolos de streaming comuns (por exemplo, MPEG DASH, Smooth, HLS) diretamente aos seus clientes ou a uma CDN (Rede de Distribuição de Conteúdo) para uma distribuição posterior.

    -ou-

    Registre e armazene o conteúdo incluído para uma transmissão posterior (Vídeo sob Demanda).

Ao fazer o streaming ao vivo, você pode escolher uma das seguintes rotas:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhando com canais que recebem a transmissão ao vivo de múltiplas taxas de bits de codificadores locais (passagem)

O diagrama a seguir mostra as partes principais da plataforma AMS que estão envolvidas no fluxo de trabalho de **passagem** .

![Fluxo de trabalho ao vivo](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhando com Canais que Recebem a Transmissão ao Vivo de Múltiplas Taxas de Bits de Codificadores Locais](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure

O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho de transmissão ao vivo em que um canal está habilitado para executar a codificação ao vivo com os serviços de mídia.

![Fluxo de trabalho ao vivo](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Para obter mais informações, consulte [trabalhando com canais habilitados a executar codificação ativa com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Para obter informações sobre a disponibilidade nos datacenters, consulte a seção [Disponibilidade](#availability).

## <a name="consuming-content"></a>Consumo de conteúdo

Os Serviços de Mídia do Azure fornecem as ferramentas necessárias para criar aplicativos de player do cliente sofisticados e dinâmicos para a maioria das plataformas, incluindo: dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox e decodificadores de sinais. O tópico a seguir fornece links para SDKs e estruturas de Player que você pode usar para desenvolver seus próprios aplicativos de cliente que podem consumir mídia de streaming dos Serviços de Mídia. Para obter mais informações, consulte [Desenvolvendo aplicativos do cliente de vídeo](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Habilitando o CDN do Azure

Os Serviços de Mídia dão suporte à integração com o CDN do Azure. Para obter informações sobre como habilitar o CDN do Azure, consulte [Como gerenciar pontos de extremidade de Streaming em uma conta de Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md).

## <a id="scaling"></a>Dimensionando uma conta dos Serviços de Mídia

Os clientes AMS podem dimensionar os pontos de extremidade do streaming, processamento de mídia e armazenamento em suas contas AMS.

* Os clientes dos Serviços de Mídia podem escolher um ponto de extremidade de streaming **Standard** ou do streaming **Premium**. Um ponto de extremidade de streaming **Standard** é adequado para a maior parte das cargas de trabalho do streaming. Isso inclui os mesmos recursos dos pontos de extremidade do streaming **Premium** e dimensiona a largura de banda de saída automaticamente. 

    Os pontos de extremidade do streaming **Premium** são adequados para as cargas de trabalho avançadas, fornecendo uma capacidade de largura de banda dimensionável e dedicada. Os clientes que têm um ponto de extremidade de streaming **Premium**, por padrão, obtêm uma US (Unidade de Streaming). O ponto de extremidade de streaming pode ser dimensionado adicionando USs. Cada US fornece uma capacidade de largura de banda adicional para o aplicativo. Para obter mais informações sobre como dimensionar os pontos de extremidade do streaming **Premium**, consulte o tópico [Dimensionando os pontos de extremidade do streaming](media-services-portal-scale-streaming-endpoints.md).

* Uma conta dos Serviços de Mídia está associada a um Tipo de Unidade Reservada que determina a velocidade com que as suas tarefas de processamento de mídia são processadas. Escolha um entre os seguintes tipos de unidade reservada: **S1**, **S2** ou **S3**. Por exemplo, o mesmo trabalho de codificação é executado mais rapidamente quando você usa o tipo de unidade reservada **S2** em comparação ao tipo **S1**.

    Além de especificar o tipo de unidade reservada, você pode especificar o provisionamento de sua conta com as **URs** (Unidades Reservadas). O número de URs provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em determinada conta.

    >[!NOTE]
    >As URs trabalham para paralelizar todo o processamento de mídia, incluindo os trabalhos de indexação, usando o Azure Media Indexer. No entanto, ao contrário da codificação, a indexação de trabalhos não será processada mais rapidamente com unidades reservadas mais rápidas.

    Para obter mais informações, consulte [Processamento de mídia de escala](media-services-portal-scale-media-processing.md).
* Você também pode dimensionar sua conta dos Serviços de Mídia adicionando contas de armazenamento a ela. Cada conta de armazenamento é limitada a 500 TB. Para expandir o armazenamento além das limitações padrão, você pode optar por anexar diversas contas de armazenamento a uma única conta de serviços de mídia. Para saber mais, consulte [Gerenciar contas de armazenamento](meda-services-managing-multiple-storage-accounts.md).

## <a id="availability"></a>Disponibilidade de recursos dos Serviços de Mídia nos datacenters

Esta seção fornece detalhes sobre a disponibilidade de recursos dos Serviços de Mídia nos datacenters.

### <a name="ams-accounts"></a>Contas AMS

#### <a name="availability"></a>Disponibilidade

Para determinar se os Serviços de Mídia estão disponíveis em um data center, navegue até https://azure.microsoft.com/status/ e role para a tabela MÍDIA.

### <a name="streaming-endpoints"></a>Pontos de extremidade de streaming 

Os clientes dos Serviços de Mídia podem escolher um ponto de extremidade de streaming **Standard** ou do streaming **Premium**. Para obter mais informações, consulte a seção sobre [dimensionamento](#scaling).

#### <a name="availability"></a>Disponibilidade

|NOME|Status|Datacenters
|---|---|---|
|Standard|GA|Todos|
|Premium|GA|Todos|

### <a name="live-encoding"></a>Codificação ativa

#### <a name="availability"></a>Disponibilidade

Disponível em todos os datacenters, exceto: Alemanha, Sul do Brasil, Índia Ocidental, Sul da Índia e Índia Central. 

### <a name="encoding-media-processors"></a>Codificando processadores de mídia

A AMS oferece dois codificadores de sob demanda **Media Encoder Standard** e **Fluxo de Trabalho do Media Encoder Premium**. Para obter mais informações, consulte [Visão geral e comparação dos codificadores de mídia sob demanda do Azure](media-services-encode-asset.md). 

#### <a name="availability"></a>Disponibilidade

|Nome do processador de mídia|Status|Datacenters
|---|---|---|
|Media Encoder Standard|GA|Todos|
|Fluxo de trabalho do Media Encoder Premium|GA|Todos, exceto China|

### <a name="analytics-media-processors"></a>Processadores de mídia da Análise

A Análise de Mídia é uma coleção de componentes de fala e pesquisa visual que facilitam a obtenção de análises acionáveis dos arquivos de vídeo de organizações e de empresas. Para saber mais, confira [Visão geral a Análise dos Serviços de Mídia do Azure](media-services-analytics-overview.md).

#### <a name="availability"></a>Disponibilidade

|Nome do processador de mídia|Status|Datacenters
|---|---|---|
|Detector de Rostos em Mídias do Azure|Visualização|Todos|
|Azure Media Hyperlapse|Visualização|Todos|
|Indexador de Mídia do Azure|GA|Todos|
|Detector de Movimento em Mídias do Azure|Visualização|Todos|
|OCR de Mídia do Azure|Visualização|Todos|
|Azure Media Redactor|Visualização|Todos|
|Azure Media Stabilizer|Visualização|Todos|
|Miniaturas de Vídeo de Mídia do Azure|Visualização|Todos|
|Azure Media Indexer 2|Visualização|Todos, exceto regiões da China e do Governo Federal|

### <a name="protection"></a>Proteção

Os Serviços de Mídia do Microsoft Azure permitem proteger a mídia desde o momento em que ela deixa computador e durante o armazenamento, processamento e entrega. Para obter mais informações, consulte [Protegendo o conteúdo AMS](media-services-content-protection-overview.md).

#### <a name="availability"></a>Disponibilidade

|Criptografia|Status|Datacenters|
|---|---|---| 
|Armazenamento|GA|Todos|
|Chaves AES-128|GA|Todos|
|FairPlay|GA|Todos|
|PlayReady|GA|Todos|
|Widevine|GA|Todos, exceto Alemanha, Governo Federal e China.

### <a name="reserved-units-rus"></a>Unidades Reservadas (URs)

O número de unidades reservadas provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. 

Para obter mais informações, consulte a seção sobre [dimensionamento](#scaling).

#### <a name="availability"></a>Disponibilidade

Disponível em todos os datacenters.

### <a name="reserved-unit-ru-type"></a>Tipo de unidade reservada (UR)

Uma conta dos Serviços de Mídia está associada a um tipo de Unidade reservada, que determina a velocidade com a qual suas tarefas de processamento de mídia são processadas. Escolha um entre os seguintes tipos de unidade reservada: S1, S2 ou S3.

Para obter mais informações, consulte a seção sobre [dimensionamento](#scaling).

#### <a name="availability"></a>Disponibilidade

|Nome do tipo de UR|Status|Datacenters
|---|---|---|
|S1|GA|Todos|
|S2|GA|Todos, exceto sul do Brasil e Índia Ocidental|
|S3|GA|Todos, exceto Índia Ocidental|

## <a name="next-steps"></a>Próximas etapas

Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

