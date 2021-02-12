---
title: Suporte ao idioma-Tradutor
titleSuffix: Azure Cognitive Services
description: O tradutor de serviços cognitivas dá suporte aos seguintes idiomas para conversão de texto em texto usando a conversão de máquina neural (NMT).
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: 935a9e92de88c2519dc1a1042315d204e8f60099
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919911"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Suporte a idiomas e regiões para conversão de texto e fala

Use o tradutor para traduzir de e para qualquer uma das mais de 70 idiomas de tradução de texto. A conversão de máquina neural (NMT) é o novo padrão para traduções de máquina com ia de alta qualidade e está disponível como o padrão usando V3 do tradutor quando um sistema neural está disponível.

Você também pode usar o Translator em conjunto com o tradutor personalizado para criar sistemas de tradução neural que compreendam a terminologia usada em seu próprio negócio e setor e com o serviço de fala da Microsoft para adicionar tradução de fala ao seu aplicativo.

[Saiba mais sobre como a tradução automática funciona](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Tradução de texto
A tradução de texto está disponível usando a operação de conversão de ou para qualquer um dos idiomas disponíveis no tradutor. A API também oferece detecção de idioma usando a operação de detecção, a transliteração usando a operação de transliteração e os dicionários bilíngües usando as operações de exemplos de dicionário e pesquisa de dicionário. Os idiomas disponíveis para cada uma dessas operações estão listados abaixo. 

### <a name="translate"></a>Tradução

O tradutor dá suporte aos seguintes idiomas para tradução de texto para texto. 

[Exibir a documentação de referência da operação de tradução](reference/v3-0-translate.md)

| Linguagem | Código de idioma |
|:-|:-:|
| Africâner | `af` |
| Árabe | `ar` |
| Assamês | `as` |
| Bangla | `bn` |
| Bósnio (latim) | `bs` |
| Búlgaro | `bg` |
| Cantonês (tradicional) | `yue` |
| Catalão | `ca` |
| Chinês simplificado | `zh-Hans` |
| Chinês (tradicional) | `zh-Hant` |
| Croata | `hr` |
| Tcheco | `cs` |
| Dari | `prs` |
| Dinamarquês | `da` |
| Holandês | `nl` |
| Inglês | `en` |
| Estoniano | `et` |
| Fijiano | `fj` |
| Filipino | `fil` |
| Finlandês | `fi` |
| Francês | `fr` |
| Francês (Canadá) | `fr-ca` |
| Alemão | `de` |
| Grego | `el` |
| Guzerate | `gu` |
| Crioulo haitiano | `ht` |
| Hebraico | `he` |
| Híndi | `hi` |
| Hmong Daw | `mww` |
| Húngaro | `hu` |
| Islandês | `is` |
| Indonésio | `id` |
| Inuktitut | `iu` |
| Irlandês | `ga` |
| Italiano | `it` |
| Japonês | `ja` |
| canarim | `kn` |
| Cazaque | `kk` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Coreano | `ko` |
| Curdo (central) | `ku` |
| Curdo (Norte) | `kmr` |
| Letão | `lv` |
| Lituano | `lt` |
| Malgaxe | `mg` |
| Malaio | `ms` |
| Malaiala | `ml` |
| Maltês | `mt` |
| Maori | `mi` |
| Marati | `mr` |
| Norueguês | `nb` |
| Oriá | `or` |
| Pashto | `ps` |
| Persa | `fa` |
| Polonês | `pl` |
| Português (Brasil) | `pt` |
| Português (Portugal) | `pt-pt` |
| Panjabi | `pa` |
| Queretaro Otomi | `otq` |
| Romeno | `ro` |
| Russo | `ru` |
| Samoano | `sm` |
| Sérvio (cirílico) | `sr-Cyrl` |
| Sérvio (latino) | `sr-Latn` |
| Eslovaco | `sk` |
| Esloveno | `sl` |
| Espanhol | `es` |
| Swahili | `sw` |
| Sueco | `sv` |
| Taitiano | `ty` |
| Tâmil | `ta` |
| Télugo | `te` |
| Tailandês | `th` |
| Tongan | `to` |
| Turco | `tr` |
| Ucraniano | `uk` |
| Urdu | `ur` |
| Vietnamita | `vi` |
| Galês | `cy` |
| Yucatec Maya | `yua` |

> [!NOTE]
> O código `pt` de idioma padrão será `pt-br` , Português (Brasil).

### <a name="detect"></a>Detect

O tradutor detecta os seguintes idiomas para tradução e transliteração.

[Exibir a documentação de referência da operação de detecção](reference/v3-0-detect.md)

| Linguagem | Código de idioma |
|:-|:-:|
| Africâner | `af` |
| Árabe | `ar` |
| Búlgaro | `bg` |
| Catalão | `ca` |
| Chinês simplificado | `zh-Hans` |
| Chinês (tradicional) | `zh-Hant` |
| Croata | `hr` |
| Tcheco | `cs` |
| Dinamarquês | `da` |
| Holandês | `nl` |
| Inglês | `en` |
| Estoniano | `et` |
| Finlandês | `fi` |
| Francês | `fr` |
| Alemão | `de` |
| Grego | `el` |
| Guzerate | `gu` |
| Crioulo haitiano | `ht` |
| Hebraico | `he` |
| Híndi | `hi` |
| Húngaro | `hu` |
| Islandês | `is` |
| Indonésio | `id` |
| Irlandês | `ga` |
| Italiano | `it` |
| Japonês | `ja` |
| Klingon | `tlh-Latn` |
| Coreano | `ko` |
| Curdo (central) | `ku-Arab` |
| Letão | `lv` |
| Lituano | `lt` |
| Malaio | `ms` |
| Maltês | `mt` |
| Norueguês | `nb` |
| Pashto | `ps` |
| Persa | `fa` |
| Polonês | `pl` |
| Português | `pt` |
| Romeno | `ro` |
| Russo | `ru` |
| Sérvio (cirílico) | `sr-Cyrl` |
| Sérvio (latino) | `sr-Latn` |
| Eslovaco | `sk` |
| Esloveno | `sl` |
| Espanhol | `es` |
| Swahili | `sw` |
| Sueco | `sv` |
| Taitiano | `ty` |
| Tailandês | `th` |
| Turco | `tr` |
| Ucraniano | `uk` |
| Urdu | `ur` |
| Vietnamita | `vi` |
| Galês | `cy` |
| Yucatec Maya | `yua` |

### <a name="transliterate"></a>Transliterate

O método Transliterate suporta os seguintes idiomas. Em "Para / De", "<->" indica que o idioma pode ser transliterado de ou para qualquer um dos scripts listados. O "->" indica que o idioma só pode ser transliterado de um script para outro.

[Exibir a documentação de referência de operação de transliteração](reference/v3-0-translate.md)


| Linguagem    | Código de idioma | script | Para/De | script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe `Arab` | <--> | Latim `Latn` |
| Assamês | `as` | Bengalês `Beng` | <--> | Latim `Latn` |
| Bangla  | `bn` | Bengalês `Beng` | <--> | Latim `Latn` |
|Bielorrusso| `be` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Búlgaro| `bg` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Chinês tradicional `Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Latim `Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Chinês simplificado `Hans` |
|Grego| `el` | Grego `Grek`  | <--> | Latim `Latn` |
| Guzerate | `gu`  | Guzerate `Gujr` | <--> | Latim `Latn` |
| Hebraico | `he` | Hebraico `Hebr` | <--> | Latim `Latn` |
| Híndi | `hi` | Devanagari `Deva` | <--> | Latim `Latn` |
| Japonês | `ja` | Japonês `Jpan` | <--> | Latim `Latn` |
| canarim | `kn` | Canarim `Knda` | <--> | Latim `Latn` |
|Cazaque| `kk` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Coreano| `ko` | Coreano `Kore`  | <--> | Latim `Latn` |
|Kyrgyz| `ky` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Macedônio| `mk` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Malaiala | `ml` | Malaiala `Mlym` | <--> | Latim `Latn` |
| Marati | `mr` | Devanagari `Deva` | <--> | Latim `Latn` |
|Mongol| `mn` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Oriá | `or` | Oriya `Orya` | <--> | Latim `Latn` |
|Persa| `fa` | Árabe `Arab`  | <--> | Latim `Latn` |
| Panjabi | `pa` | Gurmukhi `Guru`  | <--> | Latim `Latn`  |
|Russo| `ru` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Sérvio (cirílico) | `sr-Cyrl` | Cirílico `Cyrl`  | --> | Latim `Latn` |
| Sérvio (latino) | `sr-Latn` | Latim `Latn` | --> | Cirílico `Cyrl`|
|Sindhi| `sd` | Árabe `Arab`  | <--> | Latim `Latn` |
|Sinhala| `si` | Cingalês `Sinh`  | <--> | Latim `Latn` |
|Tajik| `tg` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Tâmil | `ta` | Tâmil `Taml` | <--> | Latim `Latn` |
|Tártaro| `tt` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Télugo | `te` | Télugo `Telu` | <--> | Latim `Latn` |
| Tailandês | `th` | Tailandês `Thai` | --> | Latim `Latn` |
|Ucraniano| `uk` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Urdu| `ur` | Árabe `Arab`  | <--> | Latim `Latn` |

### <a name="dictionary"></a>Dictionary

O dicionário suporta os seguintes idiomas para ou do inglês usando os métodos Lookup e Examples.

Veja a documentação de referência para as operações de [exemplos de dicionário](reference/v3-0-dictionary-examples.md) e pesquisa de [dicionário](reference/v3-0-dictionary-lookup.md) .

| Linguagem    | Código de idioma |
|:----------- |:-------------:|
| Africâner      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Catalão      | `ca`          |
| Chinês simplificado      | `zh-Hans`          |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Estoniano      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Crioulo haitiano      | `ht`          |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Islandês    | `is`  |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Klingon      | `tlh`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polonês      | `pl`          |
| Português (Brasil)     | `pt`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Swahili      | `sw`          |
| Sueco      | `sv`          |
| Tâmil      | `ta`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Acessar a lista de idiomas do tradutor programaticamente

Você pode recuperar uma lista de idiomas com suporte para o tradutor usando o método Languages. Você pode visualizar a lista por recurso, código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma suportado. Essa lista é atualizada automaticamente pelo serviço Microsoft Translator conforme novos idiomas são disponibilizados.

[Exibir documentação de referência de operação de idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os idiomas a seguir estão disponíveis para personalização de ou para o inglês usando o [Tradutor personalizado](https://aka.ms/CustomTranslator).

| Linguagem    | Código de idioma |
|:----------- |:-------------:|
|Africâner| `af`|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
|Catalão|   `ca`    |
| Chinês simplificado      | `zh-Hans`          |
|Chinês (tradicional)|   `zh-Hant`   |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Inglês    | `en`     |
| Estoniano      | `et`          |
|Fijiano|    `fj`    |
|Filipino|  `fil`   |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Guzerate| `gu`    |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Húngaro      | `hu`          |
| Islandês | `is` |
| Indonésio|   `id`    |
| Irlandês | `ga`  |
| Italiano      | `it`          |
| Japonês      | `ja`          |
|canarim|`kn`|
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malgaxe| `mg`    |
| Malaio|    `ms` |
|Maltês|   `mt`    |
| Maori| `mi`  |
| Marati| `mr`  |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polonês      | `pl`          |
| Português (Brasil) | `pt` |
| Panjabi|`pa`|
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoano|   `sm`    |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Swahili|  `sw`    |
| Sueco      | `sv`          |
|Taitiano|  `ty`    |
| Tailandês      | `th`          |
|Tongan|    `to`    |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu| `ur`    |
| Vietnamita      | `vi`          |
| Galês | `cy` |

## <a name="speech-translation"></a>Tradução de Fala
A tradução de fala está disponível usando o tradutor com o serviço de fala de serviços cognitivas. Exiba a [documentação do serviço de fala](../speech-service/index.yml) para saber mais sobre como usar a tradução de fala e exibir todas as opções de [idioma disponíveis](../speech-service/language-support.md).

### <a name="speech-to-text"></a>Conversão de fala em texto
Converta a fala em texto para traduzir para o idioma de texto de sua escolha. A conversão de fala em texto é usada para a tradução de fala para texto ou para tradução de fala para fala quando usada em conjunto com a síntese de fala.

| Idioma    |
|:----------- |
|Árabe|
|Cantonês (tradicional)|
|Catalão|
|Chinês simplificado|
|Chinês (tradicional)|
|Dinamarquês|
|Holandês|
|Inglês|
|Finlandês|
|Francês|
|Francês (Canadá)|
|Alemão|
|Guzerate|
|Híndi|
|Italiano|
|Japonês|
|Coreano|
|Marati|
|Norueguês|
|Polonês|
|Português (Brasil)|
|Português (Portugal)|
|Russo|
|Espanhol|
|Sueco|
|Tâmil|
|Télugo|
|Tailandês|
|Turco|

### <a name="text-to-speech"></a>Conversão de texto em fala
Converter texto em fala. A conversão de texto em fala é usada para adicionar saída audível de resultados de tradução ou para tradução de fala a fala quando usada com a fala em texto. 

| Idioma |
|:-|
| Árabe |
| Búlgaro |
| Cantonês (tradicional) |
| Catalão |
| Chinês simplificado |
| Chinês (tradicional) |
| Croata |
| Tcheco |
| Dinamarquês |
| Holandês |
| Inglês |
| Finlandês |
| Francês |
| Francês (Canadá) |
| Alemão |
| Grego |
| Hebraico |
| Híndi |
| Húngaro |
| Indonésio |
| Italiano |
| Japonês |
| Coreano |
| Malaio |
| Norueguês |
| Polonês |
| Português (Brasil) |
| Português (Portugal) |
| Romeno |
| Russo |
| Eslovaco |
| Esloveno |
| Espanhol |
| Sueco |
| Tâmil |
| Télugo |
| Tailandês |
| Turco |
| Vietnamita |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Exibir a lista de idiomas no site do Microsoft Translator

Para uma visão rápida dos idiomas, o site do Microsoft Translator mostra todos os idiomas com suporte do tradutor para tradução de texto e serviço de fala para tradução de fala. Esta lista não inclui informações específicas do desenvolvedor, como códigos de idioma.

[Veja a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
