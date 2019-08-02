---
title: Notas de versão do Video Indexer de serviços de mídia do Azure | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os serviços de mídia do Azure Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414221"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de versão do Video Indexer de serviços de mídia do Azure

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="june-2019"></a>Junho de 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer implantado no leste do Japão

Agora você pode criar uma conta paga Video Indexer na região leste do Japão.

### <a name="create-and-repair-account-api-preview"></a>Criar e reparar a API da conta (versão prévia)

Adicionada uma nova API que permite que você [atualize a chave ou o ponto de extremidade de conexão do serviço de mídia do Azure](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Melhorar o tratamento de erros ao carregar 

Uma mensagem descritiva é retornada em caso de configuração incorreta da conta subjacente dos serviços de mídia do Azure.

### <a name="player-timeline-keyframes-preview"></a>Linha do tempo do Player-visualização de quadros principais 

Agora você pode ver uma visualização de imagem para cada vez na linha do tempo do jogador.

### <a name="editor-semi-select"></a>Seleção semi do editor

Agora você pode ver uma visualização de todas as informações que são selecionadas como resultado da escolha de um período de insight específico no editor.

## <a name="may-2019"></a>Maio de 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Atualizar o modelo de idioma personalizado do arquivo de legenda oculta

[Criar um modelo de linguagem personalizado](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e atualizar as APIs de [modelos de linguagem personalizados](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) agora dão suporte aos formatos de arquivo VTT, Srt e TTML como entrada para modelos de linguagem.

Ao chamar a [API Update transcrição de vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), a transcrição é adicionada automaticamente. O modelo de treinamento associado ao vídeo também é atualizado automaticamente. Para obter informações sobre como personalizar e treinar seus modelos de idioma, consulte [Personalizar um modelo de linguagem com Video indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Novos formatos de transcrição de download – TXT e CSV

Além do formato de legenda oculta já suportado (SRT, VTT e TTML), Video Indexer agora dá suporte ao download da transcrição nos formatos TXT e CSV.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](video-indexer-overview.md)
