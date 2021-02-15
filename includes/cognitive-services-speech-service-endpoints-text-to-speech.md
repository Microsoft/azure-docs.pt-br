---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515078"
---
### <a name="standard-and-neural-voices"></a>Vozes padrão e neural

Use esta tabela para determinar a disponibilidade de vozes padrão e neurais por região/ponto de extremidade:

| Região | Ponto de extremidade | Vozes neurais | Vozes padrão |
|--------|----------|-----------------|---------------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Brazil South | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Centro dos EUA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Leste da Ásia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Leste dos EUA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Leste dos EUA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Centro da Índia | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Japan East | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Oeste do Japão | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Coreia Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Centro-Norte dos EUA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Norte da Europa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Centro-Sul dos Estados Unidos | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Oeste dos EUA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Não | Sim |
| Oeste dos EUA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |

> [!TIP]
> As [vozes na versão prévia](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) estão disponíveis apenas nestas três regiões: leste dos eua, Europa Ocidental e sudeste asiático.

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
