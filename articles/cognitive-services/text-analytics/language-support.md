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
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: c0d91f803822e018f4363bb78d9138e2efe16f8a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531440"
---
# <a name="text-analytics-api-v3-language-support"></a>Suporte ao idioma da API de Análise de Texto v3 

#### <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment-analysis)

| Linguagem              | Código de idioma | Suporte à v3 | Início da versão do modelo v3: |              Observações |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinês simplificado    |   `zh-hans`   |     ✓      |         01-10-2019         | `zh` também é aceito |
| Chinês tradicional   |   `zh-hant`   |    ✓      |         01-10-2019         |                    |
| Holandês                 |     `nl`      |     ✓      |         01-10-2019        |                    |
| Inglês               |     `en`      |     ✓      |         01-10-2019         |                    |
| Francês                |     `fr`      |     ✓      |         01-10-2019         |                    |
| Alemão                |     `de`      |     ✓      |         01-10-2019         |                    |
| Híndi                 |    `hi`       |     ✓      |         01-04-2020         |                    |
| Italiano               |     `it`      |     ✓      |         01-10-2019         |                    |
| Japonês              |     `ja`      |     ✓      |         01-10-2019         |                    |
| Coreano                |     `ko`      |    ✓      |         01-10-2019         |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |         01-04-2020         |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |         01-10-2019         | `pt` também é aceito |
| Espanhol               |     `es`      |     ✓      |         01-10-2019         |                    |
| Turco               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Mineração de opiniões (somente a versão prévia da v3.1)

| Linguagem              | Código de idioma | Início com a versão do modelo v3: |              Observações |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglês               |     `en`      |              01-04-2020              |                    |


#### <a name="named-entity-recognition-ner"></a>[NER (Reconhecimento de Entidade Nomeada)](#tab/named-entity-recognition)

> [!NOTE]
> * Somente as entidades "Person", "Location" e "Organization" são retornadas para as linguagens marcadas com *.

| Linguagem               | Código de idioma | Suporte à v3 | Início com a versão do modelo v3: |       Observações        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Árabe                 |     `ar`      |      ✓*    |               01-10-2019        |                    |
| Chinês simplificado     |   `zh-hans`   |     ✓      |               15-01-2021        | `zh` também é aceito |
| Chinês tradicional   |   `zh-hant`   |     ✓*      |               01-10-2019        |                    |
| Tcheco                 |     `cs`      |     ✓*      |               01-10-2019        |                    |
| Dinamarquês                |     `da`      |     ✓*      |               01-10-2019        |                    |
| Holandês                 |     `nl`      |     ✓*      |               01-10-2019        |                    |
| Inglês                |     `en`      |     ✓      |               01-10-2019        |                    |
| Finlandês               |     `fi`      |     ✓*      |               01-10-2019        |                    |
| Francês                 |     `fr`      |     ✓      |               15-01-2021        |                    |
| Alemão                 |     `de`      |     ✓      |               15-01-2021        |                    |
| Hebraico                |     `he`      |     ✓*      |               01-10-2019        |                    |
| Húngaro             |     `hu`      |     ✓*      |               01-10-2019        |                    |
| Italiano               |     `it`      |     ✓       |               15-01-2021        |                    |
| Japonês              |     `ja`      |     ✓       |               15-01-2021        |                    |
| Coreano                |     `ko`      |     ✓       |               15-01-2021        |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓*      |               01-10-2019        | `nb` também é aceito |
| Polonês                |     `pl`      |     ✓*      |               01-10-2019        |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓       |               15-01-2021        |                    |
| Português (Portugal) |    `pt-PT`    |     ✓       |               15-01-2021        | `pt` também é aceito |
| Russo              |     `ru`      |     ✓*       |               01-10-2019        |                    |
| Espanhol               |     `es`      |     ✓       |               01-04-2020        |                    |
| Sueco               |     `sv`      |     ✓*      |               01-10-2019        |                    |
| Turco               |     `tr`      |     ✓*      |               01-10-2019        |                    |

#### <a name="key-phrase-extraction"></a>[Extração de frases-chave](#tab/key-phrase-extraction)

| Linguagem              | Código de idioma |  Suporte à v3 | Disponível da versão do modelo v3 em diante: |       Observações        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Dinamarquês                |     `da`      |     ✓     |                01-10-2019                 |                    |
| Holandês                 |     `nl`      |     ✓      |                01-10-2019                 |                    |
| Inglês               |     `en`      |     ✓      |                01-10-2019                 |                    |
| Finlandês               |     `fi`      |     ✓      |                01-10-2019                 |                    |
| Francês                |     `fr`      |     ✓      |                01-10-2019                 |                    |
| Alemão                |     `de`      |     ✓      |                01-10-2019                 |                    |
| Italiano               |     `it`      |     ✓      |                01-10-2019                 |                    |
| Japonês              |     `ja`      |     ✓      |                01-10-2019                 |                    |
| Coreano                |     `ko`      |     ✓      |                01-10-2019                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` também é aceito |
| Polonês                |     `pl`      |    ✓      |                01-10-2019                 |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |                01-10-2019                 |                    |
| Português (Portugal) |    `pt-PT`    |    ✓      |                01-10-2019                 | `pt` também é aceito |
| Russo               |     `ru`      |     ✓      |                01-10-2019                 |                    |
| Espanhol               |     `es`      |     ✓      |                01-10-2019                 |                    |
| Sueco               |     `sv`      |     ✓      |                01-10-2019                 |                    |

#### <a name="entity-linking"></a>[Vinculação de entidade](#tab/entity-linking)

| Linguagem | Código de idioma |  Suporte à v3 | Disponível da versão do modelo v3 em diante: | Observações |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Inglês  |     `en`      |     ✓      |                01-10-2019                 |       |
| Espanhol  |     `es`      |    ✓      |                01-10-2019                 |       |

#### <a name="personally-identifiable-information-pii"></a>[PII (Informações de Identificação Pessoal)](#tab/pii)

| Linguagem               | Código de idioma | Suporte à v3 | Início com a versão do modelo v3: |       Observações        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinês simplificado     |   `zh-hans`   |     ✓      |               15-01-2021        | `zh` também é aceito |
| Inglês                |     `en`      |     ✓      |               2020-07-01        |                    |
| Francês                 |     `fr`      |     ✓      |               15-01-2021        |                    |
| Alemão                 |     `de`      |     ✓      |               15-01-2021        |                    |
| Italiano               |     `it`      |     ✓       |               15-01-2021        |                    |
| Japonês              |     `ja`      |     ✓       |               15-01-2021        |                    |
| Coreano                |     `ko`      |     ✓       |               15-01-2021        |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓       |               15-01-2021        |                    |
| Português (Portugal) |    `pt-PT`    |     ✓       |               15-01-2021        | `pt` também é aceito |
| Espanhol               |     `es`      |     ✓       |               01-04-2020        |                    |

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language-detection)

O API de Análise de Texto pode detectar uma ampla variedade de idiomas, variantes, dialetos e alguns idiomas regionais/culturais e retornar idiomas detectados com seu nome e código. Os parâmetros do código de idioma da Detecção de Idioma da Análise de Texto estão em conformidade com o padrão [BCP-47](https://tools.ietf.org/html/bcp47) com a maioria deles em conformidade com identificadores [ISO-639-1](https://www.iso.org/iso-639-language-codes.html). 

Se você tiver um conteúdo expresso em um idioma usado com menos frequência, experimente a Detecção de Idioma para ver se ela retorna um código. A resposta para idiomas que não pode ser detectada é `unknown`.

| Linguagem | Código de idioma | Suporte à v3 | Disponível da versão do modelo v3 em diante: |
|:-|:-:|:-:|:-:|
|Africâner|`af`|✓|    |
|Albanês|`sq`|✓|    |
|Amárico|`am`|✓|05-01-2021|
|Árabe|`ar`|✓|    |
|Armênia|`hy`|✓|    |
|Assamês|`as`|✓|05-01-2021|
|Azerbaidjano|`az`|✓|05-01-2021|
|Basco|`eu`|✓|    |
|Bielorrusso|`be`|✓|    |
|Bengali|`bn`|✓|    |
|Bósnio|`bs`|✓|01-09-2020|
|Búlgaro|`bg`|✓|    |
|Birmanês|`my`|✓|    |
|Catalão|`ca`|✓|    |
|Khmer Central|`km`|✓|    |
|Chinês|`zh`|✓|    |
|Chinês simplificado|`zh_chs`|✓|    |
|Chinês (tradicional)|`zh_cht`|✓|    |
|Corso|`co`|✓|05-01-2021|
|Croata|`hr`|✓|    |
|Tcheco|`cs`|✓|    |
|Dinamarquês|`da`|✓|    |
|Dari|`prs`|✓|01-09-2020|
|Divehi|`dv`|✓|    |
|Holandês|`nl`|✓|    |
|Inglês|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estoniano|`et`|✓|    |
|Fijiano|`fj`|✓|01-09-2020|
|Finlandês|`fi`|✓|    |
|Francês|`fr`|✓|    |
|Galego|`gl`|✓|    |
|Georgiano|`ka`|✓|    |
|Alemão|`de`|✓|    |
|Grego|`el`|✓|    |
|Guzerate|`gu`|✓|    |
|Haitiano|`ht`|✓|    |
|Hausa|`ha`|✓|05-01-2021|
|Hebraico|`he`|✓|    |
|Híndi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|01-09-2020|
|Húngaro|`hu`|✓|    |
|Islandês|`is`|✓|    |
|Igbo|`ig`|✓|05-01-2021|
|Indonésio|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irlandês|`ga`|✓|    |
|Italiano|`it`|✓|    |
|Japonês|`ja`|✓|    |
|Javanês|`jv`|✓|05-01-2021|
|canarim|`kn`|✓|    |
|Cazaque|`kk`|✓|01-09-2020|
|Quiniaruanda|`rw`|✓|05-01-2021|
|Quirguiz|`ky`|✓|05-01-2021|
|Coreano|`ko`|✓|    |
|Curdo|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latim|`la`|✓|    |
|Letão|`lv`|✓|    |
|Lituano|`lt`|✓|    |
|Luxemburguês|`lb`|✓|05-01-2021|
|Macedônio|`mk`|✓|    |
|Malgaxe|`mg`|✓|01-09-2020|
|Malaio|`ms`|✓|    |
|Malaiala|`ml`|✓|    |
|Maltês|`mt`|✓|    |
|Maori|`mi`|✓|01-09-2020|
|Marati|`mr`|✓|01-09-2020|
|Mongol|`mn`|✓|05-01-2021|
|Nepali|`ne`|✓|05-01-2021|
|Norueguês|`no`|✓|    |
|Norueguês Nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Persa|`fa`|✓|    |
|Polonês|`pl`|✓|    |
|Português|`pt`|✓|    |
|Panjabi|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|01-09-2020|
|Romeno|`ro`|✓|    |
|Russo|`ru`|✓|    |
|Samoano|`sm`|✓|01-09-2020|
|Sérvio|`sr`|✓|    |
|Shona|`sn`|✓|05-01-2021|
|Sindhi|`sd`|✓|05-01-2021|
|Sinhala|`si`|✓|    |
|Eslovaco|`sk`|✓|    |
|Esloveno|`sl`|✓|    |
|Somali|`so`|✓|    |
|Espanhol|`es`|✓|    |
|Sundanês|`su`|✓|05-01-2021|
|Swahili|`sw`|✓|    |
|Sueco|`sv`|✓|    |
|Tagalo|`tl`|✓|    |
|Taitiano|`ty`|✓|01-09-2020|
|Tajik|`tg`|✓|05-01-2021|
|Tâmil|`ta`|✓|    |
|Tártaro|`tt`|✓|05-01-2021|
|Télugo|`te`|✓|    |
|Tailandês|`th`|✓|    |
|Tibetano|`bo`|✓|05-01-2021|
|Tigrinya|`ti`|✓|05-01-2021|
|Tongan|`to`|✓|01-09-2020|
|Turco|`tr`|✓|05-01-2021|
|Turcomeno|`tk`|✓|05-01-2021|
|Xhosa|`xh`|✓|05-01-2021|
|Ioruba|`yo`|✓|05-01-2021|
|Zulu|`zu`|✓|05-01-2021|

---

## <a name="see-also"></a>Confira também

* [O que é a API de Análise de Texto?](overview.md)   
