---
title: Idiomas compatíveis – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: O Serviço de Fala é compatível com vários idiomas para conversão de fala em texto e vice-versa, bem como com a tradução de fala. Este artigo fornece uma lista abrangente de idiomas compatíveis por recurso de serviço.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 9dfd950303d1cb838d44ae672fed33dd4cd7b5ec
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381895"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Idiomas compatíveis e suporte de voz para o serviço de Fala

Os idiomas compatíveis variam de acordo com a funcionalidade do serviço de Fala. As tabelas a seguir resumem os idiomas compatíveis para as ofertas de serviço de [Conversão de fala em texto](#speech-to-text), [Conversão de texto em fala](#text-to-speech) e [Tradução de fala](#speech-translation).

## <a name="speech-to-text"></a>Conversão de fala em texto

O SDK do Microsoft Speech e a API REST são compatíveis com os idiomas (localidades) a seguir. 

Para melhorar a precisão, a personalização é oferecida para um subconjunto de idiomas por meio do upload de **Áudio + Transcrições rotuladas por humanos** ou **Texto relacionado: sentenças**. O suporte para a personalização do modelo acústico com **áudio + transcrições com rótulo humano** é limitado aos modelos de base específicos listados abaixo. Outros modelos e idiomas básicos só usarão o texto das transcrições para treinar modelos personalizados, assim como com o **texto relacionado: frases**. Para saber mais sobre a personalização, confira [Introdução à Fala Personalizada](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Idioma                 | Localidade (BCP-47) | Personalizações  | [Detecção de idioma](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Árabe (Bahrein), padrão moderno  | `ar-BH` | Modelo de linguagem                                   | Sim                           | 
| Árabe (Egito)                     | `ar-EG` | Modelo de linguagem                                   | Sim                          |
| Árabe (Iraque)                      | `ar-IQ` | Modelo de linguagem                                   |                           |
| Árabe (Israel)                    | `ar-IL` | Modelo de linguagem                                   |                           |
| Árabe (Jordânia)                    | `ar-JO` | Modelo de linguagem                                   |                           |
| Árabe (Kuwait)                    | `ar-KW` | Modelo de linguagem                                   |                           |
| Árabe (Líbano)                   | `ar-LB` | Modelo de linguagem                                   |                           |
| Árabe (Omã)                      | `ar-OM` | Modelo de linguagem                                   |                           |
| Árabe (Catar)                     | `ar-QA` | Modelo de linguagem                                   |                           |
| Árabe (Arábia Saudita)              | `ar-SA` | Modelo de linguagem                                   | Sim                          |
| Árabe (estado de Palestina)        | `ar-PS` | Modelo de linguagem                                   |                           |
| Árabe (Síria)                     | `ar-SY` | Modelo de linguagem                                   | Sim                          |
| Árabe (Emirados Árabes Unidos)      | `ar-AE` | Modelo de linguagem                                   |                           |
| Búlgaro (Bulgária)               | `bg-BG` | Modelo de linguagem                                   |                           |
| Catalão (Espanha)                    | `ca-ES` | Modelo de linguagem                                   | Sim                          |
| Chinês (Cantonês tradicional)   | `zh-HK` | Modelo acústico (20201015)<br>Modelo de linguagem                 |        Sim                   |
| Chinês (mandarim, simplificado)     | `zh-CN` | Modelo acústico (20200910)<br>Modelo de linguagem                 |     Sim                      |
| Chinês (Mandarim Taiwanês)       | `zh-TW` | Modelo acústico (20190701, 20201015)<br>Modelo de linguagem                 |           Sim                |
| Croata (Croácia)                 | `hr-HR` | Modelo de linguagem                                   |                           |
| Tcheco (República Tcheca)             | `cs-CZ` | Modelo de Linguagem                                   |                           |
| Dinamarquês (Dinamarca)                   | `da-DK` | Modelo de linguagem                                   | Sim                          |
| Holandês (Países Baixos)                | `nl-NL` | Modelo acústico (20201015)<br>Modelo de linguagem                                   |    Sim                       |
| Inglês (Austrália)                | `en-AU` | Modelo acústico (20201019)<br>Modelo de linguagem                 | Sim                          |
| Inglês (Canadá)                   | `en-CA` | Modelo acústico (20201019)<br>Modelo de linguagem                 | Sim                          |
| Inglês (Hong Kong)                | `en-HK` | Modelo de Linguagem                                   |                           |
| Inglês (Índia)                    | `en-IN` | Modelo acústico (20200923)<br>Modelo de linguagem                 | Sim                          |
| Inglês (Irlanda)                  | `en-IE` | Modelo de Linguagem                                   |                           |
| Inglês (Nova Zelândia)              | `en-NZ` | Modelo acústico (20201019)<br>Modelo de linguagem                 |  Sim                         |
| Inglês (Nigéria)                  | `en-NG` | Modelo de Linguagem                                   |                           |
| Inglês (Filipinas)              | `en-PH` | Modelo de Linguagem                                   |                           |
| Inglês (Singapura)                | `en-SG` | Modelo de Linguagem                                   |                           |
| Inglês (África do Sul)             | `en-ZA` | Modelo de Linguagem                                   |                           |
| Inglês (Reino Unido)           | `en-GB` | Modelo acústico (20201019)<br>Modelo de linguagem<br>Pronúncia| Sim                          |
| Inglês (Estados Unidos)            | `en-US` | Modelo acústico (20201019)<br>Modelo de linguagem<br>Pronúncia| Sim                          |
| Estoniano (Estônia)                  | `et-EE` | Modelo de Linguagem                                   |                           |
| Finlandês (Finlândia)                  | `fi-FI` | Modelo de linguagem                                   |     Sim                      |
| Francês (Canadá)                    | `fr-CA` | Modelo acústico (20201015)<br>Modelo de linguagem                 |     Sim                      |
| Francês (França)                    | `fr-FR` | Modelo acústico (20201015)<br>Modelo de linguagem<br>Pronúncia|      Sim                     |
| Alemão (Alemanha)                   | `de-DE` | Modelo acústico (20190701, 20200619, 20201127)<br>Modelo de linguagem<br>Pronúncia|  Sim                         |
| Grego (Grécia)                     | `el-GR` | Modelo de linguagem                                   |                           |
| Guzerate (Índia)                  | `gu-IN` | Modelo de linguagem                                   |                           |
| Híndi (Índia)                      | `hi-IN` | Modelo acústico (20200701)<br>Modelo de linguagem                 |     Sim                      |
| Húngaro (Hungria)                | `hu-HU` | Modelo de Linguagem                                   |                           |
| Irlandês (Irlanda)                     | `ga-IE` | Modelo de linguagem                                   |                           |
| Italiano (Itália)                    | `it-IT` | Modelo acústico (20201016)<br>Modelo de linguagem<br>Pronúncia|      Sim                     |
| Japonês (Japão)                   | `ja-JP` | Modelo de linguagem                                   |      Sim                     |
| Coreano (Coreia do Sul)                     | `ko-KR` | Modelo acústico (20201015)<br>Modelo de linguagem                 |      Sim                     |
| Letão (Letônia)                   | `lv-LV` | Modelo de linguagem                                   |                           |
| Lituano (Lituânia)             | `lt-LT` | Modelo de linguagem                                   |                           |
| Maltês (Malta)                     | `mt-MT` | Modelo de linguagem                                   |                           |
| Marati (Índia)                    | `mr-IN` | Modelo de linguagem                                   |                           |
| Norueguês, (Bokmål, Noruega)         | `nb-NO` | Modelo de linguagem                                   |     Sim                      |
| Polonês (Polônia)                    | `pl-PL` | Modelo de linguagem                                   |       Sim                    |
| Português (Brasil)                | `pt-BR` | Modelo acústico (20190620, 20201015)<br>Modelo de linguagem<br>Pronúncia|          Sim                 |
| Português (Portugal)              | `pt-PT` | Modelo de linguagem                                   |             Sim              |
| Romeno (Romênia)                 | `ro-RO` | Modelo de linguagem                                   |                           |
| Russo (Rússia)                   | `ru-RU` | Modelo acústico (20200907)<br>Modelo de linguagem                 |                Sim           |
| Eslovaco (Eslováquia)                  | `sk-SK` | Modelo de linguagem                                   |                           |
| Esloveno (Eslovênia)               | `sl-SI` | Modelo de linguagem                                   |                           |
| Espanhol (Argentina)                | `es-AR` | Modelo de Linguagem                                   |                           |
| Espanhol (Bolívia)                  | `es-BO` | Modelo de Linguagem                                   |                           |
| Espanhol (Chile)                    | `es-CL` | Modelo de Linguagem                                   |                           |
| Espanhol (Colômbia)                 | `es-CO` | Modelo de Linguagem                                   |                           |
| Espanhol (Costa Rica)               | `es-CR` | Modelo de Linguagem                                   |                           |
| Espanhol (Cuba)                     | `es-CU` | Modelo de Linguagem                                   |                           |
| Espanhol (República Dominicana)       | `es-DO` | Modelo de Linguagem                                   |                           |
| Espanhol (Equador)                  | `es-EC` | Modelo de Linguagem                                   |                           |
| Espanhol (El Salvador)              | `es-SV` | Modelo de Linguagem                                   |                           |
| Espanhol (Guiné Equatorial)        | `es-GQ` | Modelo de Linguagem                                   |                           |
| Espanhol (Guatemala)                | `es-GT` | Modelo de Linguagem                                   |                           |
| Espanhol (Honduras)                 | `es-HN` | Modelo de Linguagem                                   |                           |
| Espanhol (México)                   | `es-MX` | Modelo acústico (20200907)<br>Modelo de linguagem                 |    Sim                       |
| Espanhol (Nicarágua)                | `es-NI` | Modelo de Linguagem                                   |                           |
| Espanhol (Panamá)                   | `es-PA` | Modelo de Linguagem                                   |                           |
| Espanhol (Paraguai)                 | `es-PY` | Modelo de Linguagem                                   |                           |
| Espanhol (Peru)                     | `es-PE` | Modelo de Linguagem                                   |                           |
| Espanhol (Porto Rico)              | `es-PR` | Modelo de Linguagem                                   |                           |
| Espanhol (Espanha)                    | `es-ES` | Modelo acústico (20201015)<br>Modelo de linguagem                 |  Sim                         |
| Espanhol (Uruguai)                  | `es-UY` | Modelo de Linguagem                                   |                           |
| Espanhol (EUA)                      | `es-US` | Modelo de Linguagem                                   |                           |
| Espanhol (Venezuela)                | `es-VE` | Modelo de Linguagem                                   |                           |
| Sueco (Suécia)                   | `sv-SE` | Modelo de linguagem                                   |   Sim                        |
| Tâmil (Índia)                      | `ta-IN` | Modelo de linguagem                                   |                           |
| Télugo (Índia)                     | `te-IN` | Modelo de linguagem                                   |                           |
| Tailandês (Tailândia)                    | `th-TH` | Modelo de linguagem                                   |      Sim                     |
| Turco (Turquia)                   | `tr-TR` | Modelo de linguagem                                   |                           |

## <a name="text-to-speech"></a>Conversão de texto em fala

O SDK do Microsoft Speech e as APIs REST são compatíveis com essas vozes, cada uma delas compatível com um idioma e um dialeto específicos, identificados pela localidade. Você também pode obter uma lista completa de idiomas e vozes compatíveis para cada região/ponto de extremidade específico por meio da [API vozes/lista](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

Vozes neurais podem ser usadas para interagir com chatbots e assistentes de voz de maneira mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros e aprimorar sistemas de navegação veiculares. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

> [!NOTE]
> As vozes neurais são criadas a partir de exemplos que usam uma taxa de amostragem de 24 kHz.
> Todas as vozes podem fazer amostragem ou redução para outras taxas de amostra ao resumir.


| Idioma | Locale | Gênero | Nome da voz | Suporte de estilo |
|---|---|---|---|---|
| Árabe (Egito) | `ar-EG` | Feminino | `ar-EG-SalmaNeural` | Geral |
| Árabe (Egito) | `ar-EG` | Masculino | `ar-EG-ShakirNeural` <sup>Novo</sup> | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Feminino | `ar-SA-ZariyahNeural` | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Masculino | `ar-SA-HamedNeural` <sup>Novo</sup> | Geral |
| Búlgaro (Bulgária) | `bg-BG` | Feminino | `bg-BG-KalinaNeural` | Geral |
| Búlgaro (Bulgária) | `bg-BG` | Masculino | `bg-BG-BorislavNeural` <sup>Novo</sup> | Geral |
| Catalão (Espanha) | `ca-ES` | Feminino | `ca-ES-AlbaNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Feminino | `ca-ES-JoanaNeural` <sup>Novo</sup> | Geral |
| Catalão (Espanha) | `ca-ES` | Masculino | `ca-ES-EnricNeural` <sup>Novo</sup> | Geral |
| Chinês (Cantonês tradicional) | `zh-HK` | Feminino | `zh-HK-HiuGaaiNeural` | Geral |
| Chinês (Cantonês tradicional) | `zh-HK` | Feminino | `zh-HK-HiuMaanNeural` <sup>Novo</sup> | Geral |
| Chinês (Cantonês tradicional) | `zh-HK` | Masculino | `zh-HK-WanLungNeural` <sup>Novo</sup> | Geral |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoxiaoNeural` | Geral, vários estilos de voz disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoyouNeural` | Voz de criança, otimização para narração de histórias |
| Chinês (mandarim, simplificado) | `zh-CN` | Masculino | `zh-CN-YunyangNeural` | Otimizado para leitura de notícias,<br /> vários estilos de voz disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Masculino | `zh-CN-YunyeNeural` | Otimização para narração de histórias  |
| Chinês (Mandarim Taiwanês) | `zh-TW` | Feminino | `zh-TW-HsiaoChenNeural` <sup>Novo</sup> | Geral |
| Chinês (Mandarim Taiwanês) | `zh-TW` | Feminino | `zh-TW-HsiaoYuNeural` | Geral |
| Chinês (Mandarim Taiwanês) | `zh-TW` | Masculino | `zh-TW-YunJheNeural` <sup>Novo</sup> | Geral |
| Croata (Croácia) | `hr-HR` | Feminino | `hr-HR-GabrijelaNeural` | Geral |
| Croata (Croácia) | `hr-HR` | Masculino | `hr-HR-SreckoNeural` <sup>Novo</sup> | Geral |
| Tcheco (tcheco) | `cs-CZ` | Feminino | `cs-CZ-VlastaNeural` | Geral |
| Tcheco (tcheco) | `cs-CZ` | Masculino | `cs-CZ-AntoninNeural` <sup>Novo</sup> | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Feminino | `da-DK-ChristelNeural` | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Masculino | `da-DK-JeppeNeural` <sup>Novo</sup> | Geral |
| Holandês (Países Baixos) | `nl-NL` | Feminino | `nl-NL-ColetteNeural` | Geral |
| Holandês (Países Baixos) | `nl-NL` | Feminino | `nl-NL-FennaNeural` <sup>Novo</sup> | Geral |
| Holandês (Países Baixos) | `nl-NL` | Masculino | `nl-NL-MaartenNeural` <sup>Novo</sup> | Geral |
| Inglês (Austrália) | `en-AU` | Feminino | `en-AU-NatashaNeural` | Geral |
| Inglês (Austrália) | `en-AU` | Masculino | `en-AU-WilliamNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Feminino | `en-CA-ClaraNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Masculino | `en-CA-LiamNeural` <sup>Novo</sup> | Geral |
| Inglês (Índia) | `en-IN` | Feminino | `en-IN-NeerjaNeural` | Geral |
| Inglês (Índia) | `en-IN` | Masculino | `en-IN-PrabhatNeural` <sup>Novo</sup> | Geral |
| Inglês (Irlanda) | `en-IE` | Feminino | `en-IE-EmilyNeural` | Geral |
| Inglês (Irlanda) | `en-IE` | Masculino | `en-IE-ConnorNeural` <sup>Novo</sup> | Geral |
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-LibbyNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-MiaNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Masculino | `en-GB-RyanNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Feminino | `en-US-AriaNeural` | Geral, vários estilos de voz disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Inglês (Estados Unidos) | `en-US` | Feminino | `en-US-JennyNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Masculino | `en-US-GuyNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Feminino | `fi-FI-NooraNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Feminino | `fi-FI-SelmaNeural` <sup>Novo</sup> | Geral |
| Finlandês (Finlândia) | `fi-FI` | Masculino | `fi-FI-HarriNeural` <sup>Novo</sup> | Geral |
| Francês (Canadá) | `fr-CA` | Feminino | `fr-CA-SylvieNeural` | Geral |
| Francês (Canadá) | `fr-CA` | Masculino | `fr-CA-JeanNeural` | Geral |
| Francês (França) | `fr-FR` | Feminino | `fr-FR-DeniseNeural` | Geral |
| Francês (França) | `fr-FR` | Masculino | `fr-FR-HenriNeural` | Geral |
| Francês (Suíça) | `fr-CH` | Feminino | `fr-CH-ArianeNeural` | Geral |
| Francês (Suíça) | `fr-CH` | Masculino | `fr-CH-FabriceNeural` <sup>Novo</sup> | Geral |
| Alemão (Áustria) | `de-AT` | Feminino | `de-AT-IngridNeural` | Geral |
| Alemão (Áustria) | `de-AT` | Masculino | `de-AT-JonasNeural` <sup>Novo</sup> | Geral |
| Alemão (Alemanha) | `de-DE` | Feminino | `de-DE-KatjaNeural` | Geral |
| Alemão (Alemanha) | `de-DE` | Masculino | `de-DE-ConradNeural` | Geral |
| Alemão (Suíça) | `de-CH` | Feminino | `de-CH-LeniNeural` | Geral |
| Alemão (Suíça) | `de-CH` | Masculino | `de-CH-JanNeural` <sup>Novo</sup> | Geral |
| Grego (Grécia) | `el-GR` | Feminino | `el-GR-AthinaNeural` | Geral |
| Grego (Grécia) | `el-GR` | Masculino | `el-GR-NestorasNeural` <sup>Novo</sup> | Geral |
| Hebraico (Israel) | `he-IL` | Feminino | `he-IL-HilaNeural` | Geral |
| Hebraico (Israel) | `he-IL` | Masculino | `he-IL-AvriNeural` <sup>Novo</sup> | Geral |
| Híndi (Índia) | `hi-IN` | Feminino | `hi-IN-SwaraNeural` | Geral |
| Híndi (Índia) | `hi-IN` | Masculino | `hi-IN-MadhurNeural` <sup>Novo</sup> | Geral |
| Húngaro (Hungria) | `hu-HU` | Feminino | `hu-HU-NoemiNeural` | Geral |
| Húngaro (Hungria) | `hu-HU` | Masculino | `hu-HU-TamasNeural` <sup>Novo</sup> | Geral |
| Indonésio (Indonésia) | `id-ID` | Feminino | `id-ID-GadisNeural` <sup>Novo</sup> | Geral |
| Indonésio (Indonésia) | `id-ID` | Masculino | `id-ID-ArdiNeural` | Geral |
| Italiano (Itália) | `it-IT` | Feminino | `it-IT-ElsaNeural` | Geral |
| Italiano (Itália) | `it-IT` | Feminino | `it-IT-IsabellaNeural` | Geral |
| Italiano (Itália) | `it-IT` | Masculino | `it-IT-DiegoNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Feminino | `ja-JP-NanamiNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Masculino | `ja-JP-KeitaNeural` | Geral |
| Coreano (Coreia do Sul) | `ko-KR` | Feminino | `ko-KR-SunHiNeural` | Geral |
| Coreano (Coreia do Sul) | `ko-KR` | Masculino | `ko-KR-InJoonNeural` | Geral |
| Malaio (Malásia) | `ms-MY` | Feminino | `ms-MY-YasminNeural` | Geral |
| Malaio (Malásia) | `ms-MY` | Masculino | `ms-MY-OsmanNeural` <sup>Novo</sup> | Geral |
| Norueguês, (Bokmål, Noruega) | `nb-NO` | Feminino | `nb-NO-IselinNeural` | Geral |
| Norueguês, (Bokmål, Noruega) | `nb-NO` | Feminino | `nb-NO-PernilleNeural` <sup>Novo</sup> | Geral |
| Norueguês, (Bokmål, Noruega) | `nb-NO` | Masculino | `nb-NO-FinnNeural` <sup>Novo</sup> | Geral |
| Polonês (Polônia) | `pl-PL` | Feminino | `pl-PL-AgnieszkaNeural` <sup>Novo</sup> | Geral |
| Polonês (Polônia) | `pl-PL` | Feminino | `pl-PL-ZofiaNeural` | Geral |
| Polonês (Polônia) | `pl-PL` | Masculino | `pl-PL-MarekNeural` <sup>Novo</sup> | Geral |
| Português (Brasil) | `pt-BR` | Feminino | `pt-BR-FranciscaNeural` | Geral, vários estilos de voz disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Português (Brasil) | `pt-BR` | Masculino | `pt-BR-AntonioNeural` | Geral |
| Português (Portugal) | `pt-PT` | Feminino | `pt-PT-FernandaNeural` | Geral |
| Português (Portugal) | `pt-PT` | Feminino | `pt-PT-RaquelNeural` <sup>Novo</sup> | Geral |
| Português (Portugal) | `pt-PT` | Masculino | `pt-PT-DuarteNeural` <sup>Novo</sup> | Geral |
| Romeno (Romênia) | `ro-RO` | Feminino | `ro-RO-AlinaNeural` | Geral |
| Romeno (Romênia) | `ro-RO` | Masculino | `ro-RO-EmilNeural` <sup>Novo</sup> | Geral |
| Russo (Rússia) | `ru-RU` | Feminino | `ru-RU-DariyaNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Feminino | `ru-RU-SvetlanaNeural` <sup>Novo</sup> | Geral |
| Russo (Rússia) | `ru-RU` | Masculino | `ru-RU-DmitryNeural` <sup>Novo</sup> | Geral |
| Eslovaco (Eslováquia) | `sk-SK` | Feminino | `sk-SK-ViktoriaNeural` | Geral |
| Eslovaco (Eslováquia) | `sk-SK` | Masculino | `sk-SK-LukasNeural` <sup>Novo</sup> | Geral |
| Esloveno (Eslovênia) | `sl-SI` | Feminino | `sl-SI-PetraNeural` | Geral |
| Esloveno (Eslovênia) | `sl-SI` | Masculino | `sl-SI-RokNeural` <sup>Novo</sup> | Geral |
| Espanhol (México) | `es-MX` | Feminino | `es-MX-DaliaNeural` | Geral |
| Espanhol (México) | `es-MX` | Masculino | `es-MX-JorgeNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Feminino | `es-ES-ElviraNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Masculino | `es-ES-AlvaroNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Feminino | `sv-SE-HilleviNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Feminino | `sv-SE-SofieNeural` <sup>Novo</sup> | Geral |
| Sueco (Suécia) | `sv-SE` | Masculino | `sv-SE-MattiasNeural` <sup>Novo</sup> | Geral |
| Tâmil (Índia) | `ta-IN` | Feminino | `ta-IN-PallaviNeural` | Geral |
| Tâmil (Índia) | `ta-IN` | Masculino | `ta-IN-ValluvarNeural` <sup>Novo</sup> | Geral |
| Télugo (Índia) | `te-IN` | Feminino | `te-IN-ShrutiNeural` | Geral |
| Télugo (Índia) | `te-IN` | Masculino | `te-IN-MohanNeural` <sup>Novo</sup> | Geral |
| Tailandês (Tailândia) | `th-TH` | Feminino | `th-TH-AcharaNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Feminino | `th-TH-PremwadeeNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Masculino | `th-TH-NiwatNeural` <sup>Novo</sup> | Geral |
| Turco (Turquia) | `tr-TR` | Feminino | `tr-TR-EmelNeural` | Geral |
| Turco (Turquia) | `tr-TR` | Masculino | `tr-TR-AhmetNeural` <sup>Novo</sup> | Geral |
| Vietnamita (Vietnã) | `vi-VN` | Feminino | `vi-VN-HoaiMyNeural` | Geral |
| Vietnamita (Vietnã) | `vi-VN` | Masculino | `vi-VN-NamMinhNeural` <sup>Novo</sup> | Geral |

#### <a name="neural-voices-in-preview"></a>Vozes neurais em visualização

As vozes neurais a seguir estão em visualização pública. 

| Idioma                         | Locale  | Gênero | Nome da voz                             | Suporte de estilo |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaohanNeural` | Geral, vários estilos disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaomoNeural` | Geral, vários estilos e execução de função disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoruiNeural` | Voz sênior, vários estilos disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoxuanNeural` | Geral, vários estilos e execução de função disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Masculino   | `zh-CN-YunxiNeural` | Geral, vários estilos disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Estoniano (Estônia) | `et-EE` | Feminino | `et-EE-AnuNeural` | Geral |
| Estoniano (Estônia) | `et-EE` | Masculino | `et-EE-KertNeural` <sup>Novo</sup> | Geral |
| Irlandês (Irlanda) | `ga-IE` | Feminino | `ga-IE-OrlaNeural` | Geral |
| Irlandês (Irlanda) | `ga-IE` | Masculino | `ga-IE-ColmNeural` <sup>Novo</sup> | Geral |
| Letão (Letônia) | `lv-LV` | Feminino | `lv-LV-EveritaNeural` | Geral |
| Letão (Letônia) | `lv-LV` | Masculino | `lv-LV-NilsNeural` <sup>Novo</sup> | Geral |
| Lituano (Lituânia) | `lt-LT` | Feminino | `lt-LT-OnaNeural` | Geral |
| Lituano (Lituânia) | `lt-LT` | Masculino | `lt-LT-LeonasNeural` <sup>Novo</sup> | Geral |
| Maltês (Malta) | `mt-MT` | Feminino | `mt-MT-GraceNeural` | Geral |
| Maltês (Malta) | `mt-MT` | Masculino | `mt-MT-JosephNeural` <sup>Novo</sup> | Geral |

> [!IMPORTANT]
> As vozes na visualização pública estão disponíveis somente em três regiões de serviço: leste dos EUA, Europa Ocidental e sudeste asiático.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Para saber como você pode configurar e ajustar as vozes neurais, como estilos de fala, consulte [linguagem de marcação de síntese de fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> A voz `en-US-JessaNeural` foi alterada para `en-US-AriaNeural`. Se você usava a voz "Jessa", faça a conversão para "Aria".

> [!TIP]
> Você poderá continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

> [!NOTE]
> Com duas exceções, as vozes padrão são criadas a partir de exemplos que usam uma taxa de amostra de 16 kHz.
> **As vozes en-US-AriaRUS** e **en-US-GuyRUS** também são criadas a partir de exemplos que usam uma taxa de amostra de 24 kHz.
> Todas as vozes podem fazer amostragem ou redução para outras taxas de amostra ao resumir.

| Idioma | Localidade (BCP-47) | Gênero | Nome da voz |
|--|--|--|--|
| Árabe (árabe) | `ar-EG` | Feminino | `ar-EG-Hoda`|
| Árabe (Arábia Saudita) | `ar-SA` | Masculino | `ar-SA-Naayf`|
| Búlgaro (Bulgária) | `bg-BG` | Masculino | `bg-BG-Ivan`|
| Catalão (Espanha) | `ca-ES` | Feminino | `ca-ES-HerenaRUS`|
| Chinês (Cantonês tradicional) | `zh-HK` | Masculino | `zh-HK-Danny`|
| Chinês (Cantonês tradicional) | `zh-HK` | Feminino | `zh-HK-TracyRUS`|
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-HuihuiRUS`|
| Chinês (mandarim, simplificado) | `zh-CN` | Masculino | `zh-CN-Kangkang`|
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-Yaoyao`|
| Chinês (Mandarim Taiwanês) |  `zh-TW` | Feminino | `zh-TW-HanHanRUS`|
| Chinês (Mandarim Taiwanês) |  `zh-TW` | Feminino | `zh-TW-Yating`|
| Chinês (Mandarim Taiwanês) |  `zh-TW` | Masculino | `zh-TW-Zhiwei`|
| Croata (Croácia) | `hr-HR` | Masculino | `hr-HR-Matej`|
| Tcheco (República Tcheca) | `cs-CZ` | Masculino | `cs-CZ-Jakub`|
| Dinamarquês (Dinamarca) | `da-DK` | Feminino | `da-DK-HelleRUS`|
| Holandês (Países Baixos) | `nl-NL` | Feminino | `nl-NL-HannaRUS`|
| Inglês (Austrália) | `en-AU` | Feminino | `en-AU-Catherine`|
| Inglês (Austrália) | `en-AU` | Feminino | `en-AU-HayleyRUS`|
| Inglês (Canadá) | `en-CA` | Feminino | `en-CA-HeatherRUS`|
| Inglês (Canadá) | `en-CA` | Feminino | `en-CA-Linda`|
| Inglês (Índia) | `en-IN` | Feminino | `en-IN-Heera`|
| Inglês (Índia) | `en-IN` | Feminino | `en-IN-PriyaRUS`|
| Inglês (Índia) | `en-IN` | Masculino | `en-IN-Ravi`|
| Inglês (Irlanda) | `en-IE` | Masculino | `en-IE-Sean`|
| Inglês (Reino Unido) | `en-GB` | Masculino | `en-GB-George`|
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-HazelRUS`|
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-Susan`|
| Inglês (Estados Unidos) | `en-US` | Masculino | `en-US-BenjaminRUS`|
| Inglês (Estados Unidos) | `en-US` | Masculino | `en-US-GuyRUS`|
| Inglês (Estados Unidos) | `en-US` | Feminino | `en-US-AriaRUS`|
| Inglês (Estados Unidos) | `en-US` | Feminino | `en-US-ZiraRUS`|
| Finlandês (Finlândia) | `fi-FI` | Feminino | `fi-FI-HeidiRUS`|
| Francês (Canadá) | `fr-CA` | Feminino | `fr-CA-Caroline`|
| Francês (Canadá) | `fr-CA` | Feminino | `fr-CA-HarmonieRUS`|
| Francês (França) | `fr-FR` | Feminino | `fr-FR-HortenseRUS`|
| Francês (França) | `fr-FR` | Feminino | `fr-FR-Julie`|
| Francês (França) | `fr-FR` | Masculino | `fr-FR-Paul`|
| Francês (Suíça) | `fr-CH` | Masculino | `fr-CH-Guillaume`|
| Alemão (Áustria) | `de-AT` | Masculino | `de-AT-Michael`|
| Alemão (Alemanha) | `de-DE` | Feminino | `de-DE-HeddaRUS`|
| Alemão (Alemanha) | `de-DE` | Masculino | `de-DE-Stefan`|
| Alemão (Suíça) | `de-CH` | Masculino | `de-CH-Karsten`|
| Grego (Grécia) | `el-GR` | Masculino | `el-GR-Stefanos`|
| Hebraico (Israel) | `he-IL` | Masculino | `he-IL-Asaf`|
| Híndi (Índia) | `hi-IN` | Masculino | `hi-IN-Hemant`|
| Híndi (Índia) | `hi-IN` | Feminino | `hi-IN-Kalpana`|
| Húngaro (Hungria) | `hu-HU` | Masculino | `hu-HU-Szabolcs`|
| Indonésio (Indonésia) | `id-ID` | Masculino | `id-ID-Andika`|
| Italiano (Itália) | `it-IT` | Masculino | `it-IT-Cosimo`|
| Italiano (Itália) | `it-IT` | Feminino | `it-IT-LuciaRUS`|
| Japonês (Japão) | `ja-JP` | Feminino | `ja-JP-Ayumi`|
| Japonês (Japão) | `ja-JP` | Feminino | `ja-JP-HarukaRUS`|
| Japonês (Japão) | `ja-JP` | Masculino | `ja-JP-Ichiro`|
| Coreano (Coreia do Sul) | `ko-KR` | Feminino | `ko-KR-HeamiRUS`|
| Malaio (Malásia) | `ms-MY` | Masculino | `ms-MY-Rizwan`|
| Norueguês, (Bokmål, Noruega) | `nb-NO` | Feminino | `nb-NO-HuldaRUS`|
| Polonês (Polônia) | `pl-PL` | Feminino | `pl-PL-PaulinaRUS`|
| Português (Brasil) | `pt-BR` | Masculino | `pt-BR-Daniel`|
| Português (Brasil) | `pt-BR` | Feminino | `pt-BR-HeloisaRUS`|
| Português (Portugal) | `pt-PT` | Feminino | `pt-PT-HeliaRUS`|
| Romeno (Romênia) | `ro-RO` | Masculino | `ro-RO-Andrei`|
| Russo (Rússia) | `ru-RU` | Feminino | `ru-RU-EkaterinaRUS`|
| Russo (Rússia) | `ru-RU` | Feminino | `ru-RU-Irina`|
| Russo (Rússia) | `ru-RU` | Masculino | `ru-RU-Pavel`|
| Eslovaco (Eslováquia) | `sk-SK` | Masculino | `sk-SK-Filip`|
| Esloveno (Eslovênia) | `sl-SI` | Masculino | `sl-SI-Lado`|
| Espanhol (México) | `es-MX` | Feminino | `es-MX-HildaRUS`|
| Espanhol (México) | `es-MX` | Masculino | `es-MX-Raul`|
| Espanhol (Espanha) | `es-ES` | Feminino | `es-ES-HelenaRUS`|
| Espanhol (Espanha) | `es-ES` | Feminino | `es-ES-Laura`|
| Espanhol (Espanha) | `es-ES` | Masculino | `es-ES-Pablo`|
| Sueco (Suécia) | `sv-SE` | Feminino | `sv-SE-HedvigRUS`|
| Tâmil (Índia) | `ta-IN` | Masculino | `ta-IN-Valluvar`|
| Télugo (Índia) | `te-IN` | Feminino | `te-IN-Chitra`|
| Tailandês (Tailândia) | `th-TH` | Masculino | `th-TH-Pattara`|
| Turco (Turquia) | `tr-TR` | Feminino | `tr-TR-SedaRUS`|
| Vietnamita (Vietnã) | `vi-VN` | Masculino | `vi-VN-An` |

> [!IMPORTANT]
> A voz `en-US-Jessa` foi alterada para `en-US-Aria`. Se você usava a voz "Jessa", faça a conversão para "Aria".

> [!TIP]
> Você poderá continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A voz personalizada está disponível na camada padrão e neural. Os idiomas com suporte são diferentes para essas duas camadas. 

| Idioma | Locale | Standard | Neural |
|--|--|--|--|
| Chinês (mandarim, simplificado) | `zh-CN` | Sim | Sim |
| Chinês (mandarim, simplificado), bilíngüe inglês | `zh-CN` bilíngüe | Sim | Sim |
| Inglês (Austrália) | `en-AU` | Não | Sim |
| Inglês (Índia) | `en-IN` | Sim | Sim |
| Inglês (Reino Unido) | `en-GB` | Sim | Sim |
| Inglês (Estados Unidos) | `en-US` | Sim | Sim |
| Francês (Canadá) | `fr-CA` | Não | Sim |
| Francês (França) | `fr-FR` | Sim | Sim |
| Alemão (Alemanha) | `de-DE` | Sim | Sim |
| Italiano (Itália) | `it-IT` | Sim | Sim |
| Japonês (Japão) | `ja-JP` | Não | Sim |
| Coreano (Coreia do Sul) | `ko-KR` | Não | Sim |
| Português (Brasil) | `pt-BR` | Sim | Sim |
| Espanhol (México) | `es-MX` | Sim | Sim |
| Espanhol (Espanha) | `es-ES` | Não | Sim |

Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizada. Por exemplo, se os dados de gravação que você tem são falados em inglês com sotaque britânico, selecione `en-GB`.

> [!NOTE]
> Não damos suporte ao treinamento de modelo bilíngue em Voz Personalizada, exceto para o modelo bilíngue chinês-inglês. Selecione "Modelo bilíngue chinês/inglês" se você deseja treinar uma voz chinesa que também pode falar em inglês. Chinese-English treinamento de modelo bilíngüe usando o método padrão está disponível somente no Europa Setentrional e no EUA Central do Norte. O treinamento de voz neural personalizado está disponível em Sul do Reino Unido e leste dos EUA.

## <a name="speech-translation"></a>Tradução de fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem deve ser sempre da tabela de idiomas de conversão de fala em texto. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas está disponível para [síntese de fala](language-support.md#text-languages).

### <a name="text-languages"></a>Idiomas de texto

| Idioma de texto           | Código de idioma |
|:------------------------|:-------------:|
| Africâner               | `af`          |
| Árabe                  | `ar`          |
| Bangla                  | `bn`          |
| Bósnio (latim)         | `bs`          |
| Búlgaro               | `bg`          |
| Cantonês (tradicional) | `yue`         |
| Catalão                 | `ca`          |
| Chinês simplificado      | `zh-Hans`     |
| Chinês (tradicional)     | `zh-Hant`     |
| Croata                | `hr`          |
| Tcheco                   | `cs`          |
| Dinamarquês                  | `da`          |
| Holandês                   | `nl`          |
| Inglês                 | `en`          |
| Estoniano                | `et`          |
| Fijiano                  | `fj`          |
| Filipino                | `fil`         |
| Finlandês                 | `fi`          |
| Francês                  | `fr`          |
| Alemão                  | `de`          |
| Grego                   | `el`          |
| Guzerate                | `gu`          |
| Crioulo haitiano          | `ht`          |
| Hebraico                  | `he`          |
| Híndi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Húngaro               | `hu`          |
| Indonésio              | `id`          |
| Irlandês                   | `ga`          |
| Italiano                 | `it`          |
| Japonês                | `ja`          |
| canarim                 | `kn`          |
| Suaíli               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Coreano                  | `ko`          |
| Letão                 | `lv`          |
| Lituano              | `lt`          |
| Malgaxe                | `mg`          |
| Malaio                   | `ms`          |
| Malaiala               | `ml`          |
| Maltês                 | `mt`          |
| Maori                   | `mi`          |
| Marati                 | `mr`          |
| Norueguês               | `nb`          |
| Persa                 | `fa`          |
| Polonês                  | `pl`          |
| Português (Brasil)     | `pt-br`       |
| Português (Portugal)   | `pt-pt`       |
| Panjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Romeno                | `ro`          |
| Russo                 | `ru`          |
| Samoano                  | `sm`          |
| Sérvio (cirílico)      | `sr-Cyrl`     |
| Sérvio (latino)         | `sr-Latn`     |
| Eslovaco                  | `sk`          |
| Esloveno               | `sl`          |
| Espanhol                 | `es`          |
| Sueco                 | `sv`          |
| Taitiano                | `ty`          |
| Tâmil                   | `ta`          |
| Télugo                  | `te`          |
| Tailandês                    | `th`          |
| Tongan                  | `to`          |
| Turco                 | `tr`          |
| Ucraniano               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamita              | `vi`          |
| Galês                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Reconhecimento do Locutor

Veja na tabela a seguir os idiomas com suporte para as várias APIs de Reconhecimento do Locutor. Confira a [visão geral](speaker-recognition-overview.md) para obter informações adicionais sobre o Reconhecimento do Locutor.

| Idioma | Localidade (BCP-47) | Verificação dependente do texto | Verificação independente do texto | Identificação independente do texto |
|----|----|----|----|----|
|Inglês (EUA)  |  en-US  |  sim  |  sim  |  sim |
|Chinês (mandarim, simplificado) | zh-CN     |     n/a |     sim |     sim|
|Inglês (Austrália)     | en-AU     | n/a     | sim     | sim|
|Inglês (Canadá)     | en-CA     | n/a |     sim |     sim|
|Inglês (Reino Unido)     | en-GB     | n/a     | sim     | sim|
|Francês (Canadá)     | fr-CA     | n/a     | sim |     sim|
|Francês (França)     | fr-FR     | n/a     | sim     | sim|
|Alemão (Alemanha)     | de-DE     | n/a     | sim     | sim|
|Italiano | it-IT     |     n/a     | sim |     sim|
|Japonês     | ja-JP | n/a     | sim     | sim|
|Português (Brasil) | pt-BR |     n/a |     sim |     sim|
|Espanhol (México)     | es-MX     | n/a |     sim |     sim|
|Espanhol (Espanha)     | es-ES | n/a     | sim |     sim|

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer fala em C#](./get-started-speech-to-text.md?pivots=programming-language-chsarp)