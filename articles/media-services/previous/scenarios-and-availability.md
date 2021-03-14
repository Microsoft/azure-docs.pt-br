---
title: Serviços de Mídia do Microsoft Azure cenários comuns | Microsoft Docs
description: Este artigo fornece uma visão geral dos cenários de Serviços de Mídia do Microsoft Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: cb7500489cc4516b8cf44dd029c0831103dc53a8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007924"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Serviços de Mídia do Microsoft Azure cenários comuns

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. Confira a versão mais recente, [Serviços de Mídia v3](../latest/media-services-overview.md). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Os Serviços de Mídia do Microsoft Azure (AMS) permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para a entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

Este artigo mostra cenários comuns para entregar seu conteúdo ao vivo ou sob demanda.

## <a name="overview"></a>Visão geral

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Serviços de Mídia do Azure. Para obter mais informações, veja [Criar conta](media-services-portal-create-account.md).
* O ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **Executando**.

    Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta no estado **parado** . Para começar a transmitir seu conteúdo e aproveitar o empacotamento e a criptografia dinâmicos, o ponto de extremidade de streaming deve estar no estado **Em execução**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Os objetos normalmente usados durante o desenvolvimento no modelo AMS OData

A imagem a seguir mostra alguns dos objetos mais usados ao desenvolver em relação ao modelo de OData de Serviços de Mídia.

Clique na imagem para exibi-la em tamanho normal.  

[![Diagrama mostrando alguns dos objetos mais comumente usados ao desenvolver em relação ao modelo de dados de objeto dos serviços de mídia do Azure.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

Você pode exibir todo o modelo [aqui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteja o conteúdo no armazenamento e forneça mídia de streaming sem proteção (não criptografada)

![Fluxo de trabalho VoD](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Carregue um arquivo de mídia de alta qualidade em um ativo.

    Recomenda-se aplicar a opção de criptografia de armazenamento ao seu ativo para proteger o conteúdo durante o carregamento e, em repouso, no armazenamento.

1. Codifique para um conjunto de arquivos MP4 com taxa de bits adaptável.

    É recomendável aplicar a opção de criptografia de armazenamento ao ativo de saída para proteger seu conteúdo em repouso.

1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico).

    Se o ativo for armazenamento criptografado, você **deverá** configurar a política de entrega de ativos.
1. Publicar o ativo criando um localizador OnDemand.
1. Fluxo de conteúdo publicado.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger o conteúdo no armazenamento, fornecer mídia de streaming criptografada dinamicamente

![Proteger com o PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Carregue um arquivo de mídia de alta qualidade em um ativo. Aplique a opção de criptografia de armazenamento ao ativo.
1. Codifique para um conjunto de arquivos MP4 com taxa de bits adaptável. Aplique a opção de criptografia de armazenamento ao ativo de saída.
1. Crie uma chave de conteúdo de criptografia para o ativo que você quer que seja criptografado dinamicamente durante a reprodução.
1. Configure a política de autorização de chave de conteúdo.
1. Configure a política de entrega de ativos (usada pelo empacotamento dinâmico e criptografia dinâmica).
1. Publicar o ativo criando um localizador OnDemand.
1. Fluxo de conteúdo publicado.

## <a name="deliver-progressive-download"></a>Entregar o download progressivo

1. Carregue um arquivo de mídia de alta qualidade em um ativo.
1. Codificar em um único arquivo MP4.
1. Publicar o ativo criando um localizador OnDemand ou SAS. Se você estiver usando o localizador de SAS, o conteúdo será baixado do armazenamento de blobs do Azure. Você não precisa ter pontos de extremidade de streaming no estado iniciado.
1. Download progressivo de conteúdo.

## <a name="delivering-live-streaming-events"></a>Entregando eventos de streaming ao vivo

1. Inclua o conteúdo ao vivo usando diversos protocolos de streaming ao vivo (por exemplo, RTMP ou Smooth Streaming).
1. (opcionalmente) Codifique seu stream no fluxo de bits adaptável.
1. Visualize seu stream ao vivo.
1. Entregar o conteúdo por meio de:
    1. Protocolos de streaming comuns (por exemplo, MPEG DASH, Smooth, HLS) diretamente para seus clientes,
    1. Para uma CDN (rede de distribuição de conteúdo) para distribuição adicional ou
    1. Registre e armazene o conteúdo ingerido a ser transmitido posteriormente (vídeo sob demanda).

Ao fazer o streaming ao vivo, você pode escolher uma das seguintes rotas:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhando com canais que recebem a transmissão ao vivo de múltiplas taxas de bits de codificadores locais (passagem)

O diagrama a seguir mostra as partes principais da plataforma AMS que estão envolvidas no fluxo de trabalho de **passagem** .

![Diagrama que mostra as partes principais da plataforma M de uma parte envolvida no fluxo de trabalho de "passagem".](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhando com Canais que Recebem a Transmissão ao Vivo de Múltiplas Taxas de Bits de Codificadores Locais](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure

O diagrama a seguir mostra as principais partes da plataforma AMS envolvidas no fluxo de trabalho de transmissão ao vivo em que um canal está habilitado para realizar a codificação ativa com os serviços de mídia.

![Fluxo de trabalho ao vivo](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Para obter mais informações, consulte [trabalhando com canais habilitados a executar codificação ativa com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Consumo de conteúdo

Os Serviços de Mídia do Azure fornecem as ferramentas necessárias para criar aplicativos de player do cliente sofisticados e dinâmicos para a maioria das plataformas, incluindo: dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox e decodificadores de sinais.

## <a name="enabling-azure-cdn"></a>Habilitando o CDN do Azure

Os Serviços de Mídia dão suporte à integração com o CDN do Azure. Para obter informações sobre como habilitar o CDN do Azure, consulte [Como gerenciar pontos de extremidade de Streaming em uma conta de Serviços de Mídia](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Dimensionamento de uma conta de serviços de mídia

Os clientes AMS podem dimensionar os pontos de extremidade do streaming, processamento de mídia e armazenamento em suas contas AMS.

* Os clientes dos Serviços de Mídia podem escolher um ponto de extremidade de streaming **Standard** ou do streaming **Premium**. Um ponto de extremidade de streaming **Standard** é adequado para a maior parte das cargas de trabalho do streaming. Isso inclui os mesmos recursos dos pontos de extremidade do streaming **Premium** e dimensiona a largura de banda de saída automaticamente.

    Os pontos de extremidade do streaming **Premium** são adequados para as cargas de trabalho avançadas, fornecendo uma capacidade de largura de banda dimensionável e dedicada. Os clientes que têm um ponto de extremidade de streaming **Premium**, por padrão, obtêm uma US (Unidade de Streaming). O ponto de extremidade de streaming pode ser dimensionado adicionando USs. Cada US fornece uma capacidade de largura de banda adicional para o aplicativo. Para obter mais informações sobre como dimensionar os pontos de extremidade do streaming **Premium**, consulte o tópico [Dimensionando os pontos de extremidade do streaming](media-services-portal-scale-streaming-endpoints.md).

* Uma conta dos Serviços de Mídia está associada a um Tipo de Unidade Reservada que determina a velocidade com que as suas tarefas de processamento de mídia são processadas. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2** ou **S3**. Por exemplo, o mesmo trabalho de codificação é executado mais rapidamente quando você usa o tipo de unidade reservada **S2** em comparação ao tipo **S1**.

    Além de especificar o tipo de unidade reservada, você pode especificar para provisionar sua conta com **unidades reservadas** (RUs). O número de URs provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em determinada conta.

    > [!NOTE]
    > As URs trabalham para paralelizar todo o processamento de mídia, incluindo os trabalhos de indexação, usando o Azure Media Indexer. No entanto, ao contrário da codificação, a indexação de trabalhos não será processada mais rapidamente com unidades reservadas mais rápidas.

    Para obter mais informações, consulte [escala de processamento de mídia](media-services-portal-scale-media-processing.md).

* Você também pode dimensionar sua conta dos Serviços de Mídia adicionando contas de armazenamento a ela. Cada conta de armazenamento é limitada a 500 TB. Para saber mais, consulte [Gerenciar contas de armazenamento](./media-services-managing-multiple-storage-accounts.md).

## <a name="next-steps"></a>Próximas etapas

[Migrar para os Serviços de Mídia v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]