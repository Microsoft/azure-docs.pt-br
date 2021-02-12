---
title: Visão geral da transmissão ao vivo
description: Este artigo fornece uma visão geral da transmissão ao vivo usando os serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b552dcc0e6766316e55e9cdda6e462b2d4abfd2b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955912"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão ao vivo com os Serviços de Mídia do Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Azure Media Services permite entregar eventos ao vivo para seus clientes na nuvem do Azure. Para transmitir seus eventos ao vivo com os Serviços de Mídia, você precisa do seguinte:  

- Uma câmera é usada para capturar o evento ao vivo.<br/>Para obter ideias de instalação, confira [Configuração da engrenagem de vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).

    Se você não tiver acesso a uma câmera, as ferramentas como [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) poderão ser usadas para gerar um feed ao vivo de um arquivo de vídeo.
- Um codificador de vídeo ao vivo que converte sinais de uma câmera (ou outro dispositivo, como um laptop) em um feed de contribuição enviado para os Serviços de Mídia. O feed de contribuição pode incluir sinais relacionados à publicidade, como os marcadores SCTE-35.<br/>Para obter uma lista dos codificadores de transmissão ao vivo recomendados, confira [Codificadores de transmissão ao vivo](recommended-on-premises-live-encoders.md). Além disso, confira este blog: [produção de transmissão ao vivo com Obs](https://link.medium.com/ttuwHpaJeT).
- Componentes nos Serviços de Mídia, que permitem a você ingerir, visualizar, empacotar, gravar, criptografar e transmitir o evento ao vivo para seus clientes ou para um CDN para distribuição posterior.

Para clientes que procuram fornecer conteúdo a grandes públicos da Internet, recomendamos que você habilite a CDN no [ponto de extremidade de streaming](streaming-endpoint-concept.md).

Este artigo fornece uma visão geral e diretrizes de transmissão ao vivo com os serviços de mídia e links para outros artigos relevantes.
 
> [!NOTE]
> Você pode usar o [portal do Azure](https://portal.azure.com/) para gerenciar [eventos ao vivo](live-events-outputs-concept.md)v3, exibir [ativos](assets-concept.md)v3, obter informações sobre como acessar APIs. Para todas as outras tarefas de gerenciamento (por exemplo, Transformações e Trabalhos), use a [API REST](/rest/api/media/), a [CLI](/cli/azure/ams) ou um dos [SDKs](media-services-apis-overview.md#sdks) compatíveis.

## <a name="dynamic-packaging-and-delivery"></a>Empacotamento e entrega dinâmicos

Com os serviços de mídia, você pode aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md), que permite que você visualize e transmita suas transmissões ao vivo nos [formatos MPEG Dash, HLS e Smooth streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) do feed de contribuição que está sendo enviado para o serviço. Seus espectadores podem reproduzir a transmissão ao vivo com qualquer player compatível com HLS, DASH ou Smooth Streaming. Você pode usar o [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em seus aplicativos da Web ou móveis para fornecer seu fluxo em qualquer um desses protocolos.

## <a name="dynamic-encryption"></a>Criptografia dinâmica

A criptografia dinâmica permite que você criptografe dinamicamente seu conteúdo ao vivo ou sob demanda com o AES-128 ou qualquer um dos três principais sistemas de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. Para saber mais, confira [criptografia dinâmica](content-protection-overview.md).

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="dynamic-filtering"></a>Filtragem dinâmica

A filtragem dinâmica é usada para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviados para os jogadores. Para obter mais informações, consulte [filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipos de evento ao vivo

[Eventos ao vivo](/rest/api/media/liveevents) são responsáveis por ingerir e processar feeds de vídeo ao vivo. Um evento ao vivo pode ser definido como uma *passagem* (um codificador dinâmico local envia um fluxo de taxa de bits múltipla) ou uma *Codificação Ativa* (um codificador dinâmico local envia um fluxo de taxa de bits única). Para obter detalhes sobre a transmissão ao vivo nos serviços de mídia v3, consulte [eventos ao vivo e saídas dinâmicas](live-events-outputs-concept.md).

### <a name="pass-through"></a>Passagem

![Diagrama mostrando como os feeds de áudio e vídeo de um evento de passagem ao vivo são ingeridos e processados.](./media/live-streaming/pass-through.svg)

Ao usar o evento de passagem **ao vivo**, você depende de seu codificador ao vivo local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o evento ao vivo (usando o protocolo de entrada RTMP ou MP4 fragmentado). O evento ao vivo então executa os fluxos de vídeo de entrada para o empacotador dinâmico (ponto de extremidade de streaming) sem nenhuma transcodificação adicional. Esse evento ao vivo de passagem é otimizado para eventos ao vivo de execução longa ou transmissão ao vivo linear 24x365. 

### <a name="live-encoding"></a>Codificação ativa  

![Codificação ativa](./media/live-streaming/live-encoding.svg)

Ao usar a codificação de nuvem com os serviços de mídia, você configuraria seu codificador ao vivo local para enviar um vídeo de taxa de bits única como o feed de contribuição (até 32Mbps agregado) para o evento ao vivo (usando o protocolo de entrada RTMP ou MP4 fragmentado). O evento ao vivo codifica o fluxo de taxa de bits única de entrada em [fluxos de vídeo de taxa](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) de bits múltipla em diferentes resoluções para melhorar a entrega e o disponibiliza para entrega a dispositivos de reprodução por meio de protocolos padrão do setor, como MPEG-Dash, Apple http Live streaming (hls) e Microsoft Smooth streaming. 

### <a name="live-transcription-preview"></a>Transcrição ao vivo (versão prévia)

A transcrição ao vivo é um recurso que você pode usar com eventos ao vivo que são de passagem ou codificação ativa. Para obter mais informações, consulte [transcrição ao vivo](live-transcription.md). Quando esse recurso é habilitado, o serviço usa o recurso de [Conversão de Fala em Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio de entrada em texto. Esse texto é disponibilizado para entrega junto com vídeo e áudio em protocolos MPEG-DASH e HLS.

> [!NOTE]
> Atualmente, a transcrição ao vivo está disponível como um recurso de visualização no oeste dos EUA 2.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Para entender o fluxo de trabalho de transmissão ao vivo nos serviços de mídia v3, você precisa primeiro examinar e entender os seguintes conceitos: 

- [Pontos de extremidade de streaming](streaming-endpoint-concept.md)
- [Eventos e saídas ao vivo](live-events-outputs-concept.md)
- [Localizadores de streaming](streaming-locators-concept.md)

### <a name="general-steps"></a>Etapas gerais

1. Em sua conta de serviços de mídia, verifique se o **ponto de extremidade de streaming** (origem) está em execução. 
2. Crie um [evento ao vivo](live-events-outputs-concept.md). <br/>Ao criar o evento, é possível especificar sua inicialização automática. Como alternativa, você poderá iniciar o evento quando estiver pronto para iniciar o streaming.<br/> Quando a inicialização automática é definida como true, o Evento ao Vivo é iniciado logo após a criação. A cobrança começa assim que o Evento ao vivo começa a ser transmitido. Você deve chamar Stop explicitamente no recurso de evento ao vivo para interromper mais cobranças. Para obter mais informações, consulte [Live Event States e Billing](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL para enviar o feed de contribuição.<br/>Confira [Codificadores dinâmicos recomendados](recommended-on-premises-live-encoders.md).
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto de **ativo** . 

    Cada saída ao vivo é associada a um ativo, que ele usa para gravar o vídeo no contêiner de armazenamento de BLOBs do Azure associado. 
6. Crie uma **saída ao vivo** e use o nome do ativo que você criou para que o fluxo possa ser arquivado no ativo.

    As Saídas ao Vivo começam na criação e terminam quando são excluídas. Ao excluir a saída ao vivo, você não está excluindo o ativo subjacente e o conteúdo no ativo.
7. Crie um **localizador de streaming** com os [tipos de política de streaming internos](streaming-policy-concept.md).

    Para publicar a saída ao vivo, você deve criar um localizador de streaming para o ativo associado. 
8. Liste os caminhos no **localizador de streaming** para recuperar as URLs a serem usadas (elas são determinísticas).
9. Obtenha o nome do host para o **ponto de extremidade de streaming** (origem) do qual você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Se você quiser parar de tornar seu **evento ao vivo** visível, será necessário parar de transmitir o evento e excluir o **localizador de streaming**.
12. Se você tiver terminado o fluxo de eventos e deseja limpar os recursos provisionados anteriormente, siga o procedimento a seguir.

    * Pare de enviar o fluxo por push por meio do codificador.
    * Pare o evento ao vivo. Depois que o evento ao vivo for interrompido, ele não incorrerá em nenhum encargo. Quando for necessário iniciá-lo novamente ele terá a mesma URL de ingestão, portanto, você não precisará reconfigurar seu codificador.
    * Você pode parar seu ponto de extremidade de streaming, a menos que queira continuar fornecendo o arquivo morto do evento ao vivo como um fluxo sob demanda. Se o evento ao vivo estiver no estado parado, ele não incorrerá em nenhum encargo.

O ativo no qual a saída ao vivo está sendo arquivada torna-se automaticamente um ativo sob demanda quando a saída dinâmica é excluída. Você deve excluir todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Você pode usar um sinalizador opcional [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas dinâmicas ao parar. 

> [!TIP]
> Consulte o [tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md), o artigo examina o código que implementa as etapas descritas acima.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores dinâmicos recomendados](recommended-on-premises-live-encoders.md)
- [Usar um DVR de nuvem](live-event-cloud-dvr.md)
- [Comparação de recursos de tipos de eventos ao vivo](live-event-types-comparison.md)
- [Estados e cobrança](live-event-states-billing.md)
- [Latência](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Consulte o artigo [perguntas](frequently-asked-questions.md#live-streaming) frequentes.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Início rápido de transmissão ao vivo](live-events-wirecast-quickstart.md)
* [Tutorial de live streaming](stream-live-tutorial-with-api.md)
* [Diretrizes de migração dos Serviços de Mídia v2 para v3](migrate-v-2-v-3-migration-introduction.md)