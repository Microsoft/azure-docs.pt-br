---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 1eefb7097f672552956e90a19e0a7b411cae8a24
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455672"
---
### <a name="standard-and-neural-voices"></a>Vozes padrão e neural

Use esta tabela para determinar a **disponibilidade de vozes padrão** por região/ponto de extremidade:

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Centro dos EUA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Leste da Ásia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Leste dos EUA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Leste dos EUA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Centro da Índia | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Oeste do Japão | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Coreia Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Centro-Norte dos EUA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Norte da Europa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Centro-Sul dos Estados Unidos | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Oeste dos EUA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Oeste dos EUA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> As [vozes na versão prévia](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) estão disponíveis apenas nestas três regiões: leste dos eua, Europa Ocidental e sudeste asiático.

Use esta tabela para determinar a **disponibilidade de vozes neurais** por região/ponto de extremidade:

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | 
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Leste dos EUA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Centro da Índia | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Centro-Sul dos Estados Unidos | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Oeste dos EUA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

### <a name="custom-voices"></a>Vozes personalizadas

Se você tiver criado uma fonte de voz personalizada, use o ponto de extremidade que você criou. Você também pode usar os pontos de extremidade listados abaixo, substituindo pelo `{deploymentId}` pela ID de implantação do seu modelo de voz.

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sul do Brasil | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Canadá Central | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro dos EUA | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste da Ásia | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste dos EUA | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste dos EUA 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| França Central | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro da Índia | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste do Japão | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Oeste do Japão | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Coreia Central | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro-Norte dos EUA | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Norte da Europa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro-Sul dos Estados Unidos | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sudeste Asiático | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sul do Reino Unido | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Ocidental | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Oeste dos EUA | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Oeste dos EUA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Sintetização de Voz Personalizada

A tabela a seguir detalha o suporte regional para recursos personalizados de voz neural.

| Recurso | Regiões com suporte |
|---|---|
| Hospedagem de modelo de voz | Leste dos EUA, oeste dos EUA 2, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental, leste da Austrália |
| Caracteres em tempo real | Leste dos EUA, oeste dos EUA 2, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental, leste da Austrália |
| Caracteres de áudio longos | Leste dos EUA, Europa Ocidental, Sul do Reino Unido, Sudeste Asiático, Índia central |
| Treinamento de neural personalizado | Leste dos EUA, Sul do Reino Unido |

### <a name="long-audio-api"></a>API de áudio longo

A API de áudio longa está disponível em várias regiões com pontos de extremidade exclusivos.

| Região | Ponto de extremidade |
|--------|----------|
| Leste dos EUA | `https://eastus.customvoice.api.speech.microsoft.com` |
| Centro da Índia | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Sudeste Asiático | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sul do Reino Unido | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa Ocidental | `https://westeurope.customvoice.api.speech.microsoft.com` |
