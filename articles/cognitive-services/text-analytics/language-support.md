---
title: Idiomas compatíveis – API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Uma lista dos idiomas naturais compatíveis com a API de Análise de Texto. Este artigo explica quais idiomas têm suporte para cada operação.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: af7c3f85a75cfb425003b0da2af268fbba1d1d10
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092577"
---
# <a name="text-analytics-api-v3-language-support"></a>Suporte à linguagem API de Análise de Texto v3 

#### <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment-analysis)

| Linguagem              | Código de idioma | suporte a v2 | suporte v3 | Iniciando a versão do modelo V3: |              Anotações |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinês simplificado    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` também é aceito |
| Chinês tradicional   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Dinamarquês               |     `da`      |     ✓      |            |                            |                    |
| Holandês                 |     `nl`      |     ✓      |            |                            |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finlandês               |     `fi`      |     ✓      |            |                            |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grego                 |     `el`      |     ✓      |            |                            |                    |
| Híndi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polonês                |     `pl`      |     ✓      |            |                            |                    |
| Português (Brasil)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` também é aceito |
| Russo               |     `ru`      |     ✓      |            |                            |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Sueco               |     `sv`      |     ✓      |            |                            |                    |
| Turco               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Mineração de opinião (v 3.1-somente visualização)

| Linguagem              | Código de idioma | Iniciando com a versão do modelo V3: |              Anotações |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglês               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Reconhecimento de entidade nomeada (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Somente as entidades "Person", "Location" e "Organization" são retornadas para os idiomas marcados com *.

| Linguagem               | Código de idioma | suporte v3 | Iniciando com a versão do modelo V3: |       Anotações        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Árabe                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinês simplificado     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` também é aceito |
| Chinês tradicional   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Tcheco                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Dinamarquês                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Holandês                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Inglês                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finlandês               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Francês                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Alemão                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Hebraico                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Húngaro             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italiano               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japonês              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Coreano                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` também é aceito |
| Polonês                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Português (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` também é aceito |
| Russo              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Espanhol               |     `es`      |     ✓       |               2020-04-01        |                    |
| Sueco               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turco               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Extração de frases-chave](#tab/key-phrase-extraction)

| Linguagem              | Código de idioma | suporte a v2 | suporte v3 | Disponível a partir da versão do modelo V3: |       Anotações        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Dinamarquês                |     `da`      |     ✓      |      ✓     |                2019-10-01                 |                    |
| Holandês                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finlandês               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓      |                2020-07-01                 | `nb` também é aceito |
| Polonês                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` também é aceito |
| Russo               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Sueco               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Vinculação de entidade](#tab/entity-linking)

| Linguagem | Código de idioma | suporte a v2 | suporte v3 | Disponível a partir da versão do modelo V3: | Anotações |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Inglês  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Espanhol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language-detection)

O API de Análise de Texto pode detectar uma ampla gama de linguagens, variantes, dialetos e algumas linguagens regionais/culturais e retornar idiomas detectados com seu nome e código. Análise de Texto Detecção de Idioma parâmetros de código de idioma estão em conformidade com o padrão [bcp-47](https://tools.ietf.org/html/bcp47) com a maioria deles de acordo com os identificadores [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Se você tiver um conteúdo expresso em um idioma usado com menos frequência, experimente a Detecção de Idioma para ver se ela retorna um código. A resposta para idiomas que não pode ser detectada é `unknown`.

| Idioma | Código de idioma | suporte v3 | Disponível a partir da versão do modelo V3: |
|:-|:-:|:-:|:-:|
|Africâner|`af`|✓|    |
|Albanês|`sq`|✓|    |
|Amárico|`am`|✓|2021-01-05|
|Árabe|`ar`|✓|    |
|Armênia|`hy`|✓|    |
|Assamês|`as`|✓|2021-01-05|
|Azerbaidjano|`az`|✓|2021-01-05|
|Basco|`eu`|✓|    |
|Bielorrusso|`be`|✓|    |
|Bengali|`bn`|✓|    |
|Bósnio|`bs`|✓|2020-09-01|
|Búlgaro|`bg`|✓|    |
|Birmanês|`my`|✓|    |
|Catalão|`ca`|✓|    |
|Khmer central|`km`|✓|    |
|Chinês|`zh`|✓|    |
|Chinês simplificado|`zh_chs`|✓|    |
|Chinês (tradicional)|`zh_cht`|✓|    |
|Corso|`co`|✓|2021-01-05|
|Croata|`hr`|✓|    |
|Tcheco|`cs`|✓|    |
|Dinamarquês|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Holandês|`nl`|✓|    |
|Inglês|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estoniano|`et`|✓|    |
|Fijiano|`fj`|✓|2020-09-01|
|Finlandês|`fi`|✓|    |
|Francês|`fr`|✓|    |
|Galego|`gl`|✓|    |
|Georgiano|`ka`|✓|    |
|Alemão|`de`|✓|    |
|Grego|`el`|✓|    |
|Guzerate|`gu`|✓|    |
|Haitiano|`ht`|✓|    |
|Hausa|`ha`|✓|2021-01-05|
|Hebraico|`he`|✓|    |
|Híndi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Húngaro|`hu`|✓|    |
|Islandês|`is`|✓|    |
|Igbo|`ig`|✓|2021-01-05|
|Indonésio|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irlandês|`ga`|✓|    |
|Italiano|`it`|✓|    |
|Japonês|`ja`|✓|    |
|Javanês|`jv`|✓|2021-01-05|
|canarim|`kn`|✓|    |
|Cazaque|`kk`|✓|2020-09-01|
|Quiniaruanda|`rw`|✓|2021-01-05|
|Quirguistanês|`ky`|✓|2021-01-05|
|Coreano|`ko`|✓|    |
|Curdo|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latim|`la`|✓|    |
|Letão|`lv`|✓|    |
|Lituano|`lt`|✓|    |
|Luxemburguês|`lb`|✓|2021-01-05|
|Macedônio|`mk`|✓|    |
|Malgaxe|`mg`|✓|2020-09-01|
|Malaio|`ms`|✓|    |
|Malaiala|`ml`|✓|    |
|Maltês|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marati|`mr`|✓|2020-09-01|
|Mongol|`mn`|✓|2021-01-05|
|Nepali|`ne`|✓|2021-01-05|
|Norueguês|`no`|✓|    |
|Norueguês Nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Persa|`fa`|✓|    |
|Polonês|`pl`|✓|    |
|Português|`pt`|✓|    |
|Panjabi|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Romeno|`ro`|✓|    |
|Russo|`ru`|✓|    |
|Samoano|`sm`|✓|2020-09-01|
|Sérvio|`sr`|✓|    |
|Shona|`sn`|✓|2021-01-05|
|Sindhi|`sd`|✓|2021-01-05|
|Sinhala|`si`|✓|    |
|Eslovaco|`sk`|✓|    |
|Esloveno|`sl`|✓|    |
|Somali|`so`|✓|    |
|Espanhol|`es`|✓|    |
|Sundanês|`su`|✓|2021-01-05|
|Swahili|`sw`|✓|    |
|Sueco|`sv`|✓|    |
|Tagalo|`tl`|✓|    |
|Taitiano|`ty`|✓|2020-09-01|
|Tajik|`tg`|✓|2021-01-05|
|Tâmil|`ta`|✓|    |
|Tártaro|`tt`|✓|2021-01-05|
|Télugo|`te`|✓|    |
|Tailandês|`th`|✓|    |
|Tibetano|`bo`|✓|2021-01-05|
|Tigrinya|`ti`|✓|2021-01-05|
|Tongan|`to`|✓|2020-09-01|
|Turcomeno|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Ioruba|`yo`|✓|2021-01-05|
|Zulu|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Confira também

* [O que é o API de Análise de Texto?](overview.md)   
