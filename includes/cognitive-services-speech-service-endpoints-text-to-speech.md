---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172212"
---
### <a name="standard-and-neural-voices"></a>Vozes padrão e neurais

Use esta tabela para determinar a disponibilidade de vozes padrão e neurais por região/ponto de extremidade:

| Região | Ponto de extremidade | Vozes padrão | Vozes neurais |
|--------|----------|-----------------|---------------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Centro dos EUA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Ásia Oriental | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Leste dos EUA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Leste dos EUA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Centro da Índia | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Coreia Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Centro-Norte dos EUA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Norte da Europa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Centro-Sul dos Estados Unidos | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Oeste dos EUA | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Oeste dos EUA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |

### <a name="custom-voices"></a>Vozes personalizadas

Se você tiver criado uma fonte de voz personalizada, use o ponto de extremidade que você criou. Você também pode usar os pontos de extremidade listados abaixo, substituindo o `{deploymentId}` com a ID de implantação para seu modelo de voz.

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Canadá Central | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro dos EUA | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Ásia Oriental | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste dos EUA | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste dos EUA 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| França Central | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro da Índia | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste do Japão | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Coreia Central | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro-Norte dos EUA | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Norte da Europa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Centro-Sul dos Estados Unidos | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sudeste Asiático | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sul do Reino Unido | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Ocidental | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Oeste dos EUA | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Oeste dos EUA 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
