---
title: Limitações de contêiner-LUIS
titleSuffix: Azure Cognitive Services
description: Os idiomas do contêiner LUIS com suporte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002318"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitações de contêiner de Reconhecimento vocal (LUIS)

Os contêineres LUIS têm algumas limitações notáveis. De dependências sem suporte, a um subconjunto de idiomas com suporte, este artigo detalha essas restrições.

## <a name="supported-dependencies-for-latest-container"></a>Dependências com suporte para o `latest` contêiner

O contêiner LUIS mais recente dá suporte a:

* [Novos domínios pré-criados](luis-reference-prebuilt-domains.md): esses domínios voltados para a empresa incluem entidades, exemplos de declarações e padrões. Estenda esses domínios para seu próprio uso.

## <a name="unsupported-dependencies-for-latest-container"></a>Dependências sem suporte para o `latest` contêiner

Para [exportar para o contêiner](luis-container-howto.md#export-packaged-app-from-luis), você deve remover dependências sem suporte do seu aplicativo Luis. Quando você tenta exportar para o contêiner, o portal do LUIS relata esses recursos sem suporte que você precisa remover.

Você pode usar um aplicativo LUIS se ele **não inclui** nenhuma das seguintes dependências:

Configurações de aplicativo sem suporte|Detalhes|
|--|--|
|Culturas de contêiner sem suporte| Os idiomas holandês ( `nl-NL` ), japonês ( `ja-JP` ) e alemão ( `de-DE` ) só têm suporte com o [criador 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entidades sem suporte para todas as culturas|Entidade predefinida [KeyPhrase](luis-reference-prebuilt-keyphrase.md) para todas as culturas|
|Entidades sem suporte para cultura Inglês ( `en-US` )|Entidades predefinidas [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Preparação da fala|Não há suporte para dependências externas no contêiner.|
|Análise de sentimento|Não há suporte para dependências externas no contêiner.|
|Verificação Ortográfica do Bing|Não há suporte para dependências externas no contêiner.|

## <a name="languages-supported"></a>Idiomas compatíveis

Os contêineres LUIS dão suporte a um subconjunto dos [idiomas com suporte](luis-language-support.md#languages-supported) do Luis apropriado. Os contêineres LUIS são capazes de entender declarações nos seguintes idiomas:

| Idioma | Locale | Domínio predefinido | Entidade predefinida | Recomendações da lista de frases | **[Análise de texto](../text-analytics/language-support.md)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês (Estados Unidos) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Árabe (visualização-árabe moderno padrão) |`ar-AR`|❌|❌|❌|❌|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francês (França) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francês (Canadá) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Alemão |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Híndi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiano |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coreano |`ko-KR` | ✔️ | ❌ | ❌ | Somente *frase-chave* |
| Marati | `mr-IN`|❌|❌|❌|❌|
| Português (Brasil) |`pt-BR` | ✔️ | ✔️ | ✔️ | nem todas as subculturas |
| Espanhol (Espanha) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Espanhol (México)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Tâmil | `ta-IN`|❌|❌|❌|❌|
| Télugo | `te-IN`|❌|❌|❌|❌|
| Turco | `tr-TR` |✔️| ❌ | ❌ | Somente *sentimentos* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
