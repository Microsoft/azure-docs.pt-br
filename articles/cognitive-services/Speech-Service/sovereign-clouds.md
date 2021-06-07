---
title: Nuvens soberanas-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar nuvens soberanas
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98061745"
---
# <a name="speech-services-in-sovereign-clouds"></a>Serviços de fala em nuvens soberanass

## <a name="azure-government-united-states"></a>Azure governamental (Estados Unidos)

Disponível apenas para entidades do governo dos EUA e seus parceiros. Veja mais informações sobre o Azure governamental [aqui](../../azure-government/documentation-government-welcome.md) e [aqui.](../../azure-government/compare-azure-government-global-azure.md)

- **portal do Azure:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Regiões**
  - Governo dos EUA do Arizona
  - Gov. dos EUA – Virgínia
- **Tipos de preço disponíveis:**
  - Free (F0) e Standard (S0). Veja mais detalhes [aqui](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Recursos compatíveis:**
  - Conversão de fala em texto
    - Fala personalizada (adaptação do modelo acústico (AM) e do modelo de linguagem (LM))
      - [Estúdio de Fala](https://speech.azure.us/)
  - Conversão de texto em fala
    - Voz padrão
    - Voz neural
  - Conversor de fala
- **Recursos não compatíveis:**
  - Voz personalizada
- **Idiomas com suporte:**
  - Veja a lista de idiomas com suporte [aqui](language-support.md)

### <a name="endpoint-information"></a>Informações do ponto de extremidade

Esta seção contém informações de ponto de extremidade de serviços de fala para o uso com o [SDK de fala](speech-sdk.md), [a API REST de fala em texto](rest-speech-to-text.md)e [a API REST de conversão de texto em fala](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST dos serviços de fala

Os pontos de extremidade da API REST dos serviços de fala no Azure governamental têm o seguinte formato:

|  Tipo/operação da API REST | Formato do ponto de extremidade |
|--|--|
| Token de acesso | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [API REST de conversão de fala em texto v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [API REST de conversão de fala em texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [API REST conversão de texto em fala](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Substituir `<REGION_IDENTIFIER>` pelo identificador correspondente à região da sua assinatura desta tabela:

|                     | Identificador de região |
|--|--|
| **Governo dos EUA do Arizona**  | `usgovarizona` |
| **Gov. dos EUA – Virgínia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>SDK de fala

Para o SDK de fala em nuvens soberanas, você precisa usar a instanciação "do host" da `SpeechConfig` classe ou `--host` da opção da [CLI de fala](spx-overview.md). (Você também pode usar a instanciação "de ponto de extremidade" e `--endpoint` Opção da CLI de fala).

`SpeechConfig` a classe deve ser instanciada desta forma:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
A CLI de fala deve ser usada como esta (Observe a `--host` opção):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Substitua `subscriptionKey` pela sua chave de recurso de fala. Substituir `usGovHost` pela expressão correspondente à oferta de serviço necessária e à região da sua assinatura desta tabela:

|  Oferta de região/serviço | Expressão de host |
|--|--|
| **Governo dos EUA do Arizona** | |
| Conversão de fala em texto | `wss://usgovarizona.stt.speech.azure.us` |
| Conversão de Texto em Fala | `https://usgovarizona.tts.speech.azure.us` |
| **Gov. dos EUA – Virgínia** | |
| Conversão de fala em texto | `wss://usgovvirginia.stt.speech.azure.us` |
| Conversão de Texto em Fala | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure China

Disponível para organizações com presença de negócios na China. Veja mais informações sobre o Azure China [aqui.](/azure/china/overview-operations) 


- **portal do Azure:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Regiões**
  - Leste da China 2
- **Tipos de preço disponíveis:**
  - Free (F0) e Standard (S0). Veja mais detalhes [aqui](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Recursos compatíveis:**
  - Conversão de fala em texto
    - Fala personalizada (adaptação do modelo acústico (AM) e do modelo de linguagem (LM))
      - [Estúdio de Fala](https://speech.azure.cn/)
  - Conversão de texto em fala
    - Voz padrão
    - Voz neural
  - Conversor de fala
- **Recursos não compatíveis:**
  - Voz personalizada
- **Idiomas com suporte:**
  - Veja a lista de idiomas com suporte [aqui](language-support.md)

### <a name="endpoint-information"></a>Informações do ponto de extremidade

Esta seção contém informações de ponto de extremidade de serviços de fala para o uso com o [SDK de fala](speech-sdk.md), [a API REST de fala em texto](rest-speech-to-text.md)e [a API REST de conversão de texto em fala](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST dos serviços de fala

Os pontos de extremidade da API REST dos serviços de fala no Azure China têm o seguinte formato:

|  Tipo/operação da API REST | Formato do ponto de extremidade |
|--|--|
| Token de acesso | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [API REST de conversão de fala em texto v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [API REST de conversão de fala em texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [API REST conversão de texto em fala](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Substituir `<REGION_IDENTIFIER>` pelo identificador correspondente à região da sua assinatura desta tabela:

|                     | Identificador de região |
|--|--|
| **Leste da China 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>SDK de fala

Para o SDK de fala em nuvens soberanas, você precisa usar a instanciação "do host" da `SpeechConfig` classe ou `--host` da opção da [CLI de fala](spx-overview.md). (Você também pode usar a instanciação "de ponto de extremidade" e `--endpoint` Opção da CLI de fala).

`SpeechConfig` a classe deve ser instanciada desta forma:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
A CLI de fala deve ser usada como esta (Observe a `--host` opção):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Substitua `subscriptionKey` pela sua chave de recurso de fala. Substituir `azCnHost` pela expressão correspondente à oferta de serviço necessária e à região da sua assinatura desta tabela:

|  Oferta de região/serviço | Expressão de host |
|--|--|
| **Leste da China 2** | |
| Conversão de fala em texto | `wss://chinaeast2.stt.speech.azure.cn` |
| Conversão de Texto em Fala | `https://chinaeast2.tts.speech.azure.cn` |