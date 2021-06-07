---
title: Identifique e transcreve automaticamente conteúdo em vários idiomas com Video Indexer
titleSuffix: Azure Media Services
description: Este tópico demonstra como identificar e transcrever automaticamente conteúdo em vários idiomas com Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015548"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Identificar e transcrever automaticamente o conteúdo em vários idiomas

O Video Indexer dá suporte à identificação de idioma e à transcrição automáticas no conteúdo em vários idiomas. Esse processo envolve a identificação automática da linguagem falada em diferentes segmentos do áudio, o envio de cada segmento do arquivo de mídia a ser transcrita e a combinação da transcrição de volta a uma transcrição unificada. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Escolhendo a identificação multilíngüe na indexação com o portal

Você pode escolher a **detecção de vários idiomas** ao carregar e indexar seu vídeo. Como alternativa, você pode escolher a **detecção de vários idiomas**  ao reindexar o vídeo. As etapas a seguir descrevem como reindexar:

1. Navegue até o site do [Video Indexer](https://vi.microsoft.com/) e entre.
1. Vá para a página **biblioteca** e passe o mouse sobre o nome do vídeo que você deseja reindexar. 
1. No canto inferior direito, clique no botão **reindexar vídeo** . 
1. No diálogo **reindexar vídeo** , escolha **detecção de vários idiomas** na caixa suspensa **idioma da fonte de vídeo** .

    * Quando um vídeo é indexado como vários idiomas, a página Insight incluirá essa opção e um tipo de insight adicional será exibido, permitindo que o usuário veja qual segmento é transcrita em qual idioma "linguagem falada".
    * A tradução para todos os idiomas está totalmente disponível na transcrição em vários idiomas.
    * Todas as outras informações serão exibidas na linguagem mestra detectada – ou seja, a linguagem que apareceu mais no áudio.
    * A legenda codificada no Player também está disponível em vários idiomas.

![Experiência do Portal](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Escolhendo a identificação multilíngüe na indexação com a API

Ao indexar ou [reindexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) um vídeo usando a API, escolha a `multi-language detection` opção no `sourceLanguage` parâmetro.

### <a name="model-output"></a>Saída do modelo

O modelo irá recuperar todos os idiomas detectados no vídeo em uma lista

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Além disso, cada instância na seção transcrição incluirá o idioma no qual ela foi transcrita

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

* Conjunto de idiomas com suporte: Inglês, francês, alemão, espanhol.
* Suporte para conteúdo em vários idiomas com até três idiomas com suporte.
* Se o áudio contiver idiomas diferentes da lista de suporte acima, o resultado será inesperado.
* Comprimento mínimo do segmento a ser detectado para cada idioma – 15 segundos.
* O deslocamento de detecção de idioma é de 3 segundos em média.
* Espera-se que a fala seja contínua. As alternações frequentes entre linguagens podem afetar o desempenho dos modelos.
* A fala de alto-falantes não nativos pode afetar o desempenho do modelo (por exemplo, quando os alto-falantes usam sua língua nativa e alternam para outro idioma).
* O modelo foi projetado para reconhecer uma fala de conversa espontaneável com acústicas de áudio razoáveis (não comandos de voz, assinar, etc.).
* A criação e edição de projeto não está disponível atualmente para vídeos em vários idiomas.
* Os modelos de idioma personalizados não estão disponíveis ao usar a detecção de vários idiomas.
* Não há suporte para a adição de palavras-chave.
* Ao exportar arquivos de legenda oculta, a indicação de idioma não será exibida.
* A API de transcrição de atualização não dá suporte a vários arquivos de idiomas.

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)
