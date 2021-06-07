---
title: Visão geral da transmissão ao vivo
description: Este artigo fornece uma visão geral da transmissão ao vivo usando os serviços de mídia do Azure v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: f480f7821ea3443c76c551f45ac74d136f417060
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277293"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão ao vivo com os Serviços de Mídia do Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Azure Media Services permite entregar eventos ao vivo para seus clientes na nuvem do Azure. Para transmitir seus eventos ao vivo com os Serviços de Mídia, você precisa do seguinte:  

- Uma câmera é usada para capturar o evento ao vivo.<br/>Para obter ideias de instalação, confira [Configuração da engrenagem de vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).

    Se você não tiver acesso a uma câmera, ferramentas como o [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) poderão ser usadas para gerar um feed ao vivo de um arquivo de vídeo.
- Um codificador de vídeo ao vivo que converte sinais de uma câmera (ou outro dispositivo, como um laptop) em um feed de contribuição enviado para os Serviços de Mídia. O feed de contribuição pode incluir sinais relacionados à publicidade, como os marcadores SCTE-35.<br/>Para obter uma lista dos codificadores de transmissão ao vivo recomendados, confira [Codificadores de transmissão ao vivo](encode-recommended-on-premises-live-encoders.md). Além disso, confira este blog: [Produção de transmissão ao vivo com o OBS](https://link.medium.com/ttuwHpaJeT).
- Componentes nos Serviços de Mídia, que permitem a você ingerir, visualizar, empacotar, gravar, criptografar e transmitir o evento ao vivo para seus clientes ou para um CDN para distribuição posterior.

Para clientes que procuram fornecer conteúdo a grandes públicos da Internet, recomendamos a CDN seja habilitada no [ponto de extremidade de streaming](stream-streaming-endpoint-concept.md).

Este artigo fornece uma visão geral e diretrizes de transmissão ao vivo com os Serviços de Mídia, bem como links para outros artigos relevantes.
 
> [!NOTE]
> Você pode usar o [portal do Azure](https://portal.azure.com/) para gerenciar os [Eventos ao Vivo](live-event-outputs-concept.md) da v3, ver os [ativos](assets-concept.md) da v3 e obter informações sobre como acessar APIs. Para todas as outras tarefas de gerenciamento (por exemplo, Transformações e Trabalhos), use a [API REST](/rest/api/media/), a [CLI](/cli/azure/ams) ou um dos [SDKs](media-services-apis-overview.md#sdks) compatíveis.

## <a name="dynamic-packaging-and-delivery"></a>Empacotamento e entrega dinâmicos

Com os Serviços de Mídia, você pode aproveitar o [empacotamento dinâmico](encode-dynamic-packaging-concept.md), que permite visualizar e difundir as transmissões ao vivo nos [formatos MPEG-DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) por meio do feed de contribuição enviado para o serviço. Seus espectadores podem reproduzir a transmissão ao vivo com qualquer player compatível com HLS, DASH ou Smooth Streaming. Você pode usar o [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em seus aplicativos da Web ou móveis para fornecer seu fluxo em qualquer um desses protocolos.

## <a name="dynamic-encryption"></a>Criptografia dinâmica

A criptografia dinâmica permite que você criptografe dinamicamente o seu conteúdo ao vivo ou sob demanda com o AES-128 ou qualquer um dos três sistemas principais de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. Para saber mais, confira [criptografia dinâmica](drm-content-protection-concept.md).

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="dynamic-filtering"></a>Filtragem dinâmica

A filtragem dinâmica é usada para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviadas aos players. Para obter mais informações, confira [filtros e manifestos dinâmicos](filters-dynamic-manifest-concept.md).

## <a name="live-event-types"></a>Tipos de evento ao vivo

Os [eventos ao vivo](/rest/api/media/liveevents) são responsáveis pela ingestão e pelo processamento dos feeds de vídeo ao vivo. Um evento ao vivo pode ser definido como uma *passagem* (um codificador dinâmico local envia um fluxo de taxa de bits múltipla) ou uma *Codificação Ativa* (um codificador dinâmico local envia um fluxo de taxa de bits única). Para obter detalhes sobre a transmissão ao vivo nos Serviços de Mídia v3, confira [Eventos ao vivo e saídas ao vivo](live-event-outputs-concept.md).

### <a name="pass-through"></a>Passagem

![Diagrama mostrando como os feeds de áudio e vídeo de um Evento ao Vivo de passagem são ingeridos e processados.](./media/live-streaming/pass-through.svg)

Ao usar o **Evento ao Vivo** de passagem, você depende do seu codificador dinâmico local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o Evento ao Vivo (usando o protocolo de entrada RTMP ou MP4 fragmentado). Em seguida, o Evento ao Vivo executa os fluxos de vídeo de entrada para o empacotador dinâmico (Ponto de Extremidade de Streaming) sem nenhuma transcodificação adicional. Esse Evento ao Vivo de passagem é otimizado para eventos ao vivo de longa duração ou streaming ao vivo linear 24x365. 

### <a name="live-encoding"></a>Codificação ativa  

![Codificação ativa](./media/live-streaming/live-encoding.svg)

Usando a codificação de nuvem com os Serviços de Mídia, configure o seu codificador dinâmico local para enviar um vídeo de taxa de bits única como o feed de contribuição (até 32Mbps de agregação) para o Evento ao Vivo (usando o protocolo de entrada RTMP ou MP4 fragmentado). O Evento ao Vivo transcodifica o fluxo de taxa de bits única de entrada para [fluxos de vídeo com várias taxa de bits](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) em diferentes resoluções para aprimorar a entrega e disponibilizá-lo para entrega em dispositivos de reprodução por meio de protocolos padrão do setor, como MPEG-DASH, Apple HLS (HTTP Live Streaming) e Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Transcrição ao vivo (versão prévia)

A transcrição ao vivo é um recurso que você pode usar com eventos ao vivo que são de passagem ou de codificação ativa. Para obter mais informações, confira [transcrição ao vivo](live-event-live-transcription-how-to.md). Quando esse recurso é habilitado, o serviço usa o recurso de [Conversão de Fala em Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio de entrada em texto. Esse texto é disponibilizado para entrega junto com vídeo e áudio em protocolos MPEG-DASH e HLS.

> [!NOTE]
> Atualmente, a transcrição ao vivo está disponível como uma versão prévia do recurso no Oeste dos EUA 2.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Para entender o fluxo de trabalho da transmissão ao vivo nos Serviços de Mídia v3, você deverá examinar e entender os seguintes conceitos: 

- [Pontos de extremidade de streaming](stream-streaming-endpoint-concept.md)
- [Eventos e saídas ao vivo](live-event-outputs-concept.md)
- [Localizadores de streaming](stream-streaming-locators-concept.md)

### <a name="general-steps"></a>Etapas gerais

1. Na sua conta dos Serviços de Mídia, verifique se o **ponto de extremidade de streaming** (origem) está em execução. 
2. Crie um [evento ao vivo](live-event-outputs-concept.md). <br/>Ao criar o evento, é possível especificar sua inicialização automática. Como alternativa, você poderá iniciar o evento quando estiver pronto para iniciar o streaming.<br/> Quando a inicialização automática é definida como true, o Evento ao Vivo é iniciado logo após a criação. A cobrança começa assim que o Evento ao vivo começa a ser transmitido. É necessário chamar explicitamente o recurso Parar no evento ao vivo para impedir cobranças adicionais. Para saber mais, confira [cobrança e estados do evento ao vivo](live-event-states-billing-concept.md).
3. Obtenha as URLs de ingestão e configure o seu codificador local para usar a URL no envio do feed de contribuição.<br/>Confira [Codificadores dinâmicos recomendados](encode-recommended-on-premises-live-encoders.md).
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um objeto de **ativo**. 

    Cada saída ao vivo está associada a um ativo, que ela usa para gravar o vídeo no contêiner de armazenamento de blobs do Azure associado. 
6. Crie uma **saída ao vivo** e use o nome do ativo que você criou para que o fluxo possa ser arquivado no ativo.

    As Saídas ao Vivo começam na criação e terminam quando são excluídas. Ao excluir a Saída ao Vivo, você não está excluindo o ativo subjacente nem o conteúdo no ativo.
7. Crie um **localizador de streaming** com os [tipos internos da política de streaming](stream-streaming-policy-concept.md).

    Para publicar a saída ao vivo, você precisa criar um localizador de streaming para o ativo associado. 
8. Liste os caminhos no **localizador de streaming** para retornar as URLs a serem usadas (elas são determinísticas).
9. Obtenha o nome do host do **ponto de extremidade de streaming** (origem) do qual você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Caso deseje que o **evento ao vivo** deixe de ser visível, você precisará interromper a transmissão do evento e excluir o **localizador de streaming**.
12. Se você tiver terminado o fluxo de eventos e deseja limpar os recursos provisionados anteriormente, siga o procedimento a seguir.

    * Pare de enviar o fluxo por push por meio do codificador.
    * Pare o evento ao vivo. Depois que o evento ao vivo estiver parado, ele não incorrerá em nenhum encargo. Quando for necessário iniciá-lo novamente ele terá a mesma URL de ingestão, portanto, você não precisará reconfigurar seu codificador.
    * Você pode parar seu ponto de extremidade de streaming, a menos que queira continuar fornecendo o arquivo morto do evento ao vivo como um fluxo sob demanda. Se o evento ao vivo estiver no estado interrompido, ele não incorrerá em nenhum encargo.

O ativo para o qual a saída ao vivo está sendo arquivada torna-se automaticamente um ativo sob demanda quando a saída ao vivo é excluída. Você precisa excluir todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Você pode usar um sinalizador opcional [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas ao vivo na parada. 

> [!TIP]
> Confira o artigo [Tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md), que examina o código que implementa as etapas descritas acima.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores dinâmicos recomendados](encode-recommended-on-premises-live-encoders.md)
- [Usar um DVR de nuvem](live-event-cloud-dvr-time-how-to.md)
- [Comparação de recursos dos tipos de evento ao vivo](live-event-types-comparison-reference.md)
- [Estados e cobrança](live-event-states-billing-concept.md)
- [Latência](live-event-latency-reference.md)

## <a name="live-streaming-questions"></a>Perguntas sobre a transmissão ao vivo

Confira o artigo [Perguntas sobre a transmissão ao vivo](questions-collection.md#live-streaming).
