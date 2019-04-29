---
title: Visão geral da transmissão ao vivo usando os Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico fornece uma visão geral da transmissão ao vivo usando os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a9d0daaacb046df7943202775adc77bc912cce11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217535"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Visão geral da transmissão ao vivo usando os Serviços de Mídia

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo não darão mais suporte ao protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Faça a migração de RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="overview"></a>Visão geral

Ao distribuir eventos de transmissão ao vivo com os Serviços de Mídia do Azure, normalmente, os seguintes componentes estão envolvidos:

* Uma câmera é usada para transmitir um evento.
* Um codificador de vídeo ao vivo que converte os sinais da câmera para fluxos que são enviados a um serviço de transmissão ao vivo.

    Opcionalmente, vários codificadores sincronizados em tempo real. Para determinados eventos ao vivo críticos que exigem alta disponibilidade e qualidade de experiência muito altas, é recomendável utilizar codificadores redundantes ativos-ativos com sincronização de tempo para atingir um failover contínuo sem perda de dados.
* Um serviço de streaming ao vivo que permite que você faça o seguinte:

  * inclusão de conteúdo ao vivo usando diversos protocolos de transmissão ao vivo (por exemplo RTMP ou Smooth Streaming),
  * (opcionalmente) codificação de seu fluxo no fluxo de taxa de bits adaptável
  * visualização de sua transmissão ao vivo,
  * armazenamento do conteúdo incluído para ser transmitido posteriormente (vídeo sob demanda)
  * fornecimento do conteúdo por meio de protocolos de transmissão comuns (por exemplo, MPEG DASH, Smooth, HLS) diretamente aos seus clientes ou para uma CDN (Rede de Distribuição de Conteúdo) para a distribuição posterior.

O AMS **(Serviços de Mídia do Microsoft Azure)** fornece a capacidade de ingerir, codificar, visualizar, armazenar e fornecer o conteúdo de transmissão ao vivo.

Ao fornecer conteúdo aos clientes, sua meta é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para fazer isso, use codificadores ativos para codificar sua transmissão em uma transmissão de vídeo de taxa de bits múltipla (taxa de bits adaptável).  Para lidar com streaming em diferentes dispositivos, use o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Serviços de Mídia para reempacotar dinamicamente seu fluxo para diferentes protocolos. Os Serviços de Mídia permitem a entrega das seguintes tecnologias de streaming de taxa de bits adaptável: HLS (HTTP Live Streaming), Smooth Streaming e MPEG-DASH.

Nos Serviços de Mídia do Azure, **Canais** e **Programas**, e **StreamingEndpoints** tratam de todas as funcionalidades de transmissão ao vivo, incluindo ingestão, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline para o processamento de conteúdo de transmissão ao vivo. Um Canal pode receber fluxos de entrada ao vivo da seguinte maneira:

* Um codificador ativo local envia múltiplas taxas de bits **RTMP** ou **Smooth Streaming** (MP4 fragmentado) para o Canal que está configurado para a entrega de **passagem**. A entrega de **passagem** ocorre quando as transmissões ingeridas passam pelos **Canai**s sem nenhum processamento adicional. Você pode usar os codificadores dinâmicos a seguir, que produzem Smooth Streaming com múltiplas taxas de bits: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores dinâmicos produzem RTMP: Transcodificadores Adobe FMLE (Flash Media Live Encoder), Telestream Wirecast, Haivision, Teradek e Tricaster.  Um codificador ativo também pode enviar uma transmissão de taxa de bits única para um canal que não está habilitado para a codificação ativa, porém, isso não é recomendado. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

  > [!NOTE]
  > Usar um método de passagem é a maneira mais econômica de fazer uma transmissão ao vivo quando você estiver fazendo vários eventos durante um longo período e já tiver investido em codificadores locais. Veja os detalhes de [preços](https://azure.microsoft.com/pricing/details/media-services/) .
  > 
  > 
* Um codificador dinâmico local envia um fluxo de taxa de bits única para o Canal que está habilitado para executar a codificação ativa com os Serviços de Mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Os seguintes codificadores dinâmicos com saída RTMP são conhecidos por funcionar com canais desse tipo: Telestream Wirecast e FMLE. O Canal então realiza a codificação ao vivo do fluxo de entrada com taxa de bits única em um fluxo de vídeo (adaptável) de múltiplas taxas de bits. Quando solicitado, os Serviços de Mídia transmitem o fluxo aos clientes.

A partir da versão 2.10 dos Serviços de Mídia, quando você cria um canal, você pode especificar de que modo você deseja que o canal receba o fluxo de entrada e se deseja ou não que o canal realize a codificação ao vivo do seu fluxo. Você tem duas opções:

* **Nenhum** (passagem) – Especifique esse valor se você planejar usar um codificador ativo local que emitirá uma transmissão de taxa de bits múltipla (uma transmissão de passagem). Nesse caso, o fluxo de entrada foi transmitido para a saída sem qualquer codificação. Esse é o comportamento de um canal em versão anterior à 2.10.  
* **Standard** – Escolha esse valor se você pretende usar os Serviços de Mídia para codificar sua transmissão ao vivo de taxa de bits única para uma transmissão de múltiplas taxas de bits. Esse método é mais econômico para escalar verticalmente de forma rápida para eventos incomuns. Lembre-se de que há um impacto de cobrança para codificação ativa e você deve se lembrar que deixar um canal de codificação ativo no estado "Em execução" incorrerá em cobranças.  É recomendável parar imediatamente seus canais em execução após a conclusão do evento de streaming ativo para evitar cobranças por hora extra.

## <a name="comparison-of-channel-types"></a>Comparação dos tipos de canais

A tabela a seguir fornece um guia de comparação dos dois tipos de Canal com suporte nos Serviços de Mídia

| Recurso | Canal de passagem | Canal padrão |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não  |Sim |
| Resolução máxima, número de camadas |1080p, 8 camadas, 60+fps |720p, 6 camadas, 30 fps |
| Protocolos de entrada |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Preço |Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo” |Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo de execução máximo |24x7 |8 horas |
| Suporte para inserção de imagens fixas |Não  |Sim |
| Suporte para sinalização de anúncios |Não  |Sim |
| Legendas CEA 608/708 de passagem |Sim |Sim |
| Suporte para GOPs de entrada não uniforme |Sim |Não – a entrada deve ser GOPs de 2 s fixos |
| Suporte para entrada de taxa de quadros variável |Sim |Não – a entrada deve ser uma taxa de quadros fixa.<br/>Pequenas variações são toleradas, por exemplo, durante cenas ricas em movimento. No entanto, o codificador não poderá reduzir para 10 quadros por segundo. |
| Desligamento automático de Canais quando há perda do feed de entrada |Não  |Após 12 horas, se não houver nenhum Programa em execução |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhando com canais que recebem a transmissão ao vivo de taxa de bits múltipla de codificadores locais (passagem)

O diagrama a seguir mostra as partes principais da plataforma AMS que estão envolvidas no fluxo de trabalho de **passagem** .

![Fluxo de trabalho ao vivo](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhando com Canais que Recebem a Transmissão ao Vivo de Múltiplas Taxas de Bits de Codificadores Locais](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure

O diagrama a seguir mostra as partes principais da plataforma AMS envolvidas no fluxo de trabalho de transmissão ao vivo em que um canal está habilitado para executar a codificação ao vivo com os serviços de mídia.

![Fluxo de trabalho ao vivo](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para obter mais informações, consulte [trabalhando com canais habilitados a executar codificação ativa com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Descrição de um Canal e seus componentes relacionados

### <a name="channel"></a>Canal

Nos Serviços de Mídia, [Canais](https://docs.microsoft.com/rest/api/media/operations/channel)são responsáveis pelo processamento de conteúdo de transmissão ao vivo. Um Canal fornece um ponto de extremidade de entrada (URL de ingestão) que você então fornece a um transcodificador ao vivo. O canal recebe transmissões ao vivo de entrada do transcodificador ao vivo e o torna disponível para streaming por meio de um ou mais StreamingEndpoints. Canais também fornecem um ponto de extremidade de visualização prévia (URL de visualização prévia) que você usa para visualizar e validar seu fluxo antes de processamento e da entrega.

Você pode obter a URL de ingestão e a URL de visualização prévia quando você cria o canal. Para obter essas URLs, o canal não precisa estar no estado iniciado. Quando estiver pronto para começar a enviar dados de um transcodificador ao vivo para o canal, o canal deve ser iniciado. Depois que o transcodificador ao vivo inicia a ingestão de dados, você pode visualizar o fluxo.

Cada conta dos Serviços de Mídia pode conter vários canais, vários programas e vários StreamingEndpoints. Dependendo das necessidades de largura de banda e segurança, serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode executar pull de qualquer canal.

Ao criar um Canal, você poderá especificar os endereços IP permitidos em um dos seguintes formatos: Endereço IPv4 com 4 números e intervalo de endereços CIDR.

### <a name="program"></a>Programa
Um [Programa](https://docs.microsoft.com/rest/api/media/operations/program) permite que você controle a publicação e o armazenamento de segmentos em um fluxo ao vivo. Os canais gerenciam os programas. A relação entre canal e programa é muito semelhante à mídia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento programado naquele canal.
Você pode especificar o número de horas pelo qual deseja manter o conteúdo gravado para o programa, definindo a propriedade **ArchiveWindowLength** . Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas.

ArchiveWindowLength também determina que a quantidade máxima de clientes de tempo pode buscar de volta no tempo a partir da posição atual em tempo real. Programas podem ser executados sobre o período de tempo especificado, mas o conteúdo que sair do comprimento da janela será continuamente descartado. Esse valor desta propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um ativo. Para publicar o programa, você deve criar um localizador para o ativo associado. Ter esse localizador permitirá que você crie uma URL de transmissão que você pode fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar diversos arquivos no mesmo fluxo de entrada. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Por exemplo, o requisito de negócios é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para fazer isso, você precisa criar dois programas em execução simultânea. Um programa é definido para arquivar 6 horas do evento, mas o programa não é publicado. Outro programa é definido para 10 minutos e esse programa é publicado.

## <a name="billing-implications"></a>Implicações de cobrança
Um canal começará a ser cobrado assim que seu estado mudar para "Em execução" por meio da API.  

A tabela a seguir mostra como os estados de Canal são mapeados para os estados de cobrança na API e no Portal do Azure. Observe que os estados são ligeiramente diferentes entre a API e o Portal UX. Assim que um canal estiver no estado "Em execução" por meio da API ou no estado "Pronto" ou "Streaming" no Portal do Azure, a cobrança estará ativa.

Para parar a cobranças adicionais do Canal, você terá de Parar o Canal por meio da API ou no Portal do Azure.
Você é responsável por parar seus canais quando terminar com o canal. A falha ao interromper o canal resultará em cobrança contínua.

> [!NOTE]
> Ao trabalhar com canais Standard, o AMS desligará automaticamente qualquer Canal que ainda esteja no estado "Em execução" 12 horas depois que a transmissão de entrada for perdida e não houver nenhum Programa em execução. No entanto, você ainda será cobrado pelo tempo em que o canal estava no estado "Em execução".
>
>

### <a id="states"></a>Os estados de canal e como eles são mapeados para o modo de cobrança
O estado atual de um canal. Os valores possíveis incluem:

* **Parado**. Esse é o estado inicial do Canal após sua criação (a menos que o início automático tenha sido selecionado no portal). Não há cobrança nesse estado. Nesse estado, as propriedades do canal podem ser atualizadas, mas streaming não é permitido.
* **Iniciando**. O canal está sendo iniciado. Não há cobrança nesse estado. Nenhuma atualização ou streaming é permitido durante esse estado. Se ocorrer um erro, o canal retorna para o estado Parado.
* **Executando**. O canal é capaz de processar transmissões ao vivo. Agora o uso está sendo cobrado. Você deve parar o canal para evitar a cobrança adicional.
* **Parando**. O canal está sendo parado. Não haverá cobrança nesse estado transitório. Nenhuma atualização ou streaming é permitido durante esse estado.
* **Excluindo**. O canal está sendo excluído. Não haverá cobrança nesse estado transitório. Nenhuma atualização ou streaming é permitido durante esse estado.

A tabela a seguir mostra como os estados de canal são mapeados para o modo de cobrança.

| Estado do Canal | Indicadores da interface do usuário do portal | Trata-se de cobrança? |
| --- | --- | --- |
| Iniciando |Iniciando |Nenhum (estado transitório) |
| Executando |Pronto (nenhum programa em execução)<br/>ou o<br/>Streaming (há pelo menos um programa em execução) |SIM |
| Parando |Parando |Nenhum (estado transitório) |
| Parado |Parado |Não  |

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Especificação de ingestão dinâmica de MP4 fragmentado dos Serviços de Mídia do Azure](media-services-fmp4-live-ingest-overview.md)

[Trabalhando com canais habilitados a executar codificação ao vivo com os Serviços de Mídia do Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhando com Canais que recebam transmissão ao vivo de múltiplas taxas de bits de codificadores locais](media-services-live-streaming-with-onprem-encoders.md)

[Cotas e limitações](media-services-quotas-and-limitations.md).  

[Conceitos de Serviços de Mídia](media-services-concepts.md)
