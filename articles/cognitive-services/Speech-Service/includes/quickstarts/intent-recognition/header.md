---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052986"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço LUIS (Reconhecimento vocal) para reconhecer intenções em dados de áudio capturados de um microfone. Especificamente, você usará o SDK de Fala para capturar fala e um domínio predefinido do LUIS para identificar intenções de automação doméstica, assim como acender e apagar uma lâmpada. 

Depois de cumprir alguns pré-requisitos, é necessário seguir apenas algumas etapas para identificar intenções por meio de um microfone:

> [!div class="checklist"]
>
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Crie um objeto `IntentRecognizer` usando o objeto `SpeechConfig` acima.
> * Com o objeto `IntentRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `IntentRecognitionResult` retornado.

