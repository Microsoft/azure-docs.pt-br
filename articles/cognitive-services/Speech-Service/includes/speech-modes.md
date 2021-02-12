---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: dc569050b78a5797808f2e2e000019ba516ba22e
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739222"
---
**Interativo**
- Destinado para cenários de comando e controle.
- Tem um valor de tempo limite de segmentação de X.
- No final de um expressão reconhecido, o serviço para de processar o áudio dessa ID de solicitação e termina a rodada. A conexão não está fechada.
- O limite máximo para o reconhecimento é 20s.
- A chamada carbono típica para Invoke é `RecognizeOnceAsync` .

**Escreve**
- Destinado a reconhecimentos mais longos.
- Tem um valor de tempo limite de segmentação de Y. (Y! = X)
- Processará várias declarações completas sem encerrar a rodada.
- Terminará a opção de muita silêncio.
- O carbono continuará com uma nova ID de solicitação e repetirá o áudio conforme necessário.
- O serviço forçará a desconexão após 10 minutos de reconhecimento de fala.
- O carbono reconectará e repetirá o áudio não confirmado.
- Chamado em carbono com `StartContinuousRecognition` .

**Comandos**
- Permite que os usuários especifiquem a pontuação falando nisso.
- Chamado em carbono especificando `EnableDictation` no objeto, `SpeechConfig` independentemente da chamada à API que inicia o reconhecimento.
- O cluster de parte 1<sup>St</sup> retorna `speech.fragment` mensagens para resultados intermediários,<sup></sup> os 3 mensagens de retorno de terceiros `speech.hypothesis` .