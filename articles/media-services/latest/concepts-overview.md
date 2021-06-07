---
title: Conceitos e terminologia dos serviços de mídia
description: Saiba mais sobre a terminologia e os conceitos dos serviços de mídia do Azure.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 309ef8baada22f59b1395164626dd664366cb4d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581126"
---
# <a name="media-services-terminology-and-concepts"></a>Conceitos e terminologia dos serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tópico fornece uma breve visão geral da terminologia e dos conceitos dos serviços de mídia do Azure. O artigo também fornece links para artigos com uma explicação detalhada dos conceitos e da funcionalidade dos serviços de mídia v3.

Os conceitos fundamentais descritos nestes tópicos devem ser examinados antes de iniciar o desenvolvimento.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia dos serviços de mídia v3

|Termo|Descrição|
|---|---|
|Evento ao vivo|Um **evento ao vivo** representa um pipeline para ingestão, transcodificação (opcionalmente) e empacotamento de fluxos ao vivo de vídeo, áudio e metadados em tempo real.<br/><br/>Para clientes que migram de APIs do Media Services v2, o **evento ao vivo** substitui a entidade de **canal** na v2. Para obter mais informações, consulte [migrando de v2 para v3](migrate-v-2-v-3-migration-introduction.md).|
|Ponto de extremidade/empacotamento/origem de streaming|Um **ponto de extremidade de streaming** representa um serviço de empacotamento e origem (just-in-time) dinâmico que pode entregar seu conteúdo ao vivo e sob demanda diretamente a um aplicativo de player de cliente. Ele usa um dos protocolos de mídia de streaming comuns (HLS ou DASH). Além disso, o **ponto de extremidade de streaming** fornece criptografia dinâmica (just-in-time) para sistemas de gerenciamento de direitos digitais (DRMs) líderes do setor.<br/><br/>No setor de streaming de mídia, esse serviço é comumente conhecido como um **empacotador** ou **origem**.  Outros termos comuns do setor para esse recurso incluem JITP (just-in-time-Packager) ou JITE (codificação just-in-time).

## <a name="media-services-v3-concepts"></a>Conceitos do Media Services V3

|Conceitos|Descrição|Links|
|---|---|---|
|Ativos e carregando conteúdo|Para começar a gerenciar, criptografar, codificar, analisar e transmitir conteúdo de mídia no Azure, você precisa criar uma conta de serviços de mídia e carregar seus arquivos digitais em **ativos**.|[Upload e armazenamento na nuvem](storage-account-concept.md)<br/><br/>[Conceito de ativos](assets-concept.md)|
|Codificando o conteúdo|Depois de carregar seus arquivos de mídia digital de alta qualidade em ativos, você pode codificá-los em formatos que podem ser reproduzidos em uma ampla variedade de navegadores e dispositivos. <br/><br/>Para codificar com os serviços de mídia v3, você precisa criar **transformações** e **trabalhos**.|[Transformações e trabalhos](transforms-jobs-concept.md)<br/><br/>[Codificação com os Serviços de Mídia do Microsoft Azure](encoding-concept.md)|
|Análise de conteúdo (Video Indexer)|Os serviços de mídia v3 permitem que você extraia informações de seus arquivos de vídeo e áudio usando as predefinições do Media Services V3. Para analisar seu conteúdo usando as predefinições dos serviços de mídia v3, você precisa criar **transformações** e **trabalhos**.<br/><br/>Caso deseje insights mais detalhados, use o [Video Indexer](../video-indexer/index.yml) diretamente.|[Analisando os arquivos de áudio e vídeos](analyzing-video-audio-files-concept.md)|
|Empacotamento e entrega|Depois que o conteúdo é codificado, você pode tirar proveito do **empacotamento dinâmico**. Nos serviços de mídia, um **ponto de extremidade de streaming** é o serviço de empacotamento dinâmico usado para fornecer conteúdo de mídia aos players cliente. Para disponibilizar vídeos no ativo de saída para os clientes para reprodução, você precisa criar um **localizador de streaming** e, em seguida, criar URLs de streaming. <br/><br/>Ao criar o **localizador de streaming**, além do nome do ativo, você precisa especificar a **política de streaming**. **As políticas de streaming** permitem definir protocolos de streaming e opções de criptografia (se houver) para seus **localizadores de streaming**. O empacotamento dinâmico é usado se você transmite seu conteúdo ao vivo ou sob demanda. <br/><br/>Você pode usar **manifestos dinâmicos** dos serviços de mídia para transmitir apenas uma rendição ou subclipes específicos do seu vídeo.|[Empacotamento dinâmico](dynamic-packaging-overview.md)<br/><br/>[Pontos de extremidade de streaming](streaming-endpoint-concept.md)<br/><br/>[Localizadores de streaming](streaming-locators-concept.md)<br/><br/>[Políticas de streaming](streaming-policy-concept.md)<br/><br/>[Manifestos dinâmicos](filters-dynamic-manifest-overview.md)<br/><br/>[Filtros](filters-concept.md)|
|Proteção de conteúdo|Com os serviços de mídia, você pode entregar seu conteúdo ao vivo e sob demanda criptografado dinamicamente com criptografia AES (AES-128) ou/e qualquer um dos três principais sistemas DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. <br/><br/>Se especificar as opções de criptografia em seu fluxo, crie a **política de chave de conteúdo** e associe-a ao seu **localizador de streaming**. A **política de chave de conteúdo** permite que você configure como a chave de conteúdo é entregue aos clientes finais.<br/><br/> Tente reutilizar as políticas sempre que as mesmas opções forem necessárias.| [Políticas de chave de conteúdo](content-key-policy-concept.md)<br/><br/>[Proteção de conteúdo](content-protection-overview.md)|
|Transmissão ao vivo|Os Serviços de Mídia permitem que você entregue eventos ao vivo para seus clientes na nuvem do Azure. **Eventos ao Vivo** são responsáveis pela ingestão e pelo processamento dos feeds de vídeo ao vivo. Quando você cria um **evento ao vivo**, é criado um ponto de extremidade de entrada que pode ser usado para enviar um sinal ao vivo de um codificador remoto. Depois que o fluxo fluir para o **evento ao vivo**, você poderá iniciar o evento de streaming criando um **ativo**, uma **saída ao vivo** e um **localizador de streaming**. A **saída ao vivo** arquivará o fluxo no **ativo** e o tornará disponível para os visualizadores por meio do ponto de **extremidade de streaming**. Um evento ao vivo pode ser definido como uma *passagem* (um codificador dinâmico local envia um fluxo de taxa de bits múltipla) ou uma *Codificação Ativa* (um codificador dinâmico local envia um fluxo de taxa de bits única). |[Visão geral da transmissão ao vivo](live-streaming-overview.md)<br/><br/>[Eventos ao Vivo e Saídas Dinâmicas](live-events-outputs-concept.md)|
|Monitoramento com a grade de eventos|Para ver o progresso do trabalho, use a **grade de eventos**. Os serviços de mídia também emite os tipos de evento ao vivo. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. |[Lidando com os eventos da Grade de Eventos](monitoring/reacting-to-media-services-events.md)<br/><br/>[Esquemas](monitoring/media-services-event-schemas.md)|
|Monitoramento com o Azure Monitor|Monitore métricas e logs de diagnóstico que ajudam você a entender como seus aplicativos estão sendo executados com o Azure Monitor.|[Métrica e logs de diagnóstico](monitoring/monitor-media-services-data-reference.md)<br/><br/>[Esquemas de logs de diagnóstico](monitoring/monitor-media-services-data-reference.md)|
|Clientes do Player|Você pode usar Player de Mídia do Azure para reproduzir conteúdo de mídia transmitido pelos serviços de mídia em uma ampla variedade de navegadores e dispositivos. O Player de Mídia do Azure usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável. |[Visão geral do Player de Mídia do Azure](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Codificar o arquivo remoto e o fluxo de vídeo – REST](stream-files-tutorial-with-rest.md)
* [Codificar o arquivo carregado e o fluxo de vídeo – .NET](stream-files-tutorial-with-api.md)
* [Transmitir ao vivo – .NET](stream-live-tutorial-with-api.md)
* [Analisar vídeo – .NET](analyze-videos-tutorial-with-api.md)
* [Criptografia dinâmica AES-128 – .NET](protect-with-aes128.md)
* [Criptografar dinamicamente com vários DRM – .NET](protect-with-drm.md)