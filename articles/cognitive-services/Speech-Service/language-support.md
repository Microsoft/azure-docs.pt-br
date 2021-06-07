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
ms.openlocfilehash: 52dd642c661aa60157876a89d41c771cabfe2f1d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256150"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Idiomas compatíveis e suporte de voz para o serviço de Fala

Os idiomas compatíveis variam de acordo com a funcionalidade do serviço de Fala. As tabelas a seguir resumem os idiomas compatíveis para as ofertas de serviço de [Conversão de fala em texto](#speech-to-text), [Conversão de texto em fala](#text-to-speech) e [Tradução de fala](#speech-translation).

## <a name="speech-to-text"></a>Conversão de fala em texto

O SDK do Microsoft Speech e a API REST são compatíveis com os idiomas (localidades) a seguir. 

Para melhorar a precisão, a personalização é oferecida para um subconjunto de idiomas por meio do upload de **Áudio + Transcrições rotuladas por humanos** ou **Texto relacionado: sentenças**. O suporte para a personalização do modelo acústico com **Áudio + Transcrições Literais** é limitado aos modelos base específicos listados abaixo. Outros modelos base e idiomas só usarão o texto das transcrições para treinar modelos personalizados, assim como com o **Texto Relacionado: Frases**. Para saber mais sobre a personalização, confira [Introdução à Fala Personalizada](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Idioma                 | Localidade (BCP-47) | Personalizações  | [Detecção de idioma](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Árabe (Bahrein), padrão moderno  | `ar-BH` | Texto                                   |                           | 
| Árabe (Egito)                     | `ar-EG` | Texto                                   | Sim                          |
| Árabe (Iraque)                      | `ar-IQ` | Texto                                   |                           |
| Árabe (Israel)                    | `ar-IL` | Texto                                   |                           |
| Árabe (Jordânia)                    | `ar-JO` | Texto                                   |                           |
| Árabe (Kuwait)                    | `ar-KW` | Texto                                   |                           |
| Árabe (Líbano)                   | `ar-LB` | Texto                                   |                           |
| Árabe (Omã)                      | `ar-OM` | Texto                                   |                           |
| Árabe (Catar)                     | `ar-QA` | Texto                                   |                           |
| Árabe (Arábia Saudita)              | `ar-SA` | Texto                                   |                           |
| Árabe (Estado da Palestina)        | `ar-PS` | Texto                                   |                           |
| Árabe (Síria)                     | `ar-SY` | Texto                                   |                           |
| Árabe (Emirados Árabes Unidos)      | `ar-AE` | Texto                                   |                           |
| Búlgaro (Bulgária)               | `bg-BG` | Texto                                   |                           |
| Catalão (Espanha)                    | `ca-ES` | Texto                                   | Sim                          |
| Chinês (Cantonês tradicional)   | `zh-HK` | Áudio (20201015)<br>Texto                 |        Sim                   |
| Chinês (mandarim, simplificado)     | `zh-CN` | Áudio (20200910)<br>Texto                 |     Sim                      |
| Chinês (Mandarim Taiwanês)       | `zh-TW` | Áudio (20190701, 20201015)<br>Texto                 |           Sim                |
| Croata (Croácia)                 | `hr-HR` | Texto                                   |                           |
| Tcheco (República Tcheca)             | `cs-CZ` | Texto                                   |                           |
| Dinamarquês (Dinamarca)                   | `da-DK` | Texto                                   | Sim                          |
| Holandês (Países Baixos)                | `nl-NL` | Áudio (20201015)<br>Texto<br>Pronúncia|    Sim                       |
| Inglês (Austrália)                | `en-AU` | Áudio (20201019)<br>Texto                 | Sim                          |
| Inglês (Canadá)                   | `en-CA` | Áudio (20201019)<br>Texto                 | Sim                          |
| Inglês (Gana)                    | `en-GH` | Texto                                   |                           |
| Inglês (Hong Kong)                | `en-HK` | Texto                                   |                           |
| Inglês (Índia)                    | `en-IN` | Áudio (20200923)<br>Texto                 |                          |
| Inglês (Irlanda)                  | `en-IE` | Texto                                   |                           |
| Inglês (Quênia)                    | `en-KE` | Texto                                   |                           |
| Inglês (Nova Zelândia)              | `en-NZ` | Áudio (20201019)<br>Texto                 |                          |
| Inglês (Nigéria)                  | `en-NG` | Texto                                   |                           |
| Inglês (Filipinas)              | `en-PH` | Texto                                   |                           |
| Inglês (Singapura)                | `en-SG` | Texto                                   |                           |
| Inglês (África do Sul)             | `en-ZA` | Texto                                   |                           |
| Inglês (Tanzânia)                 | `en-TZ` | Texto                                   |                           |
| Inglês (Reino Unido)           | `en-GB` | Áudio (20201019)<br>Texto<br>Pronúncia| Sim                          |
| Inglês (Estados Unidos)            | `en-US` | Áudio (20201019)<br>Texto<br>Pronúncia| Sim                          |
| Estoniano (Estônia)                  | `et-EE` | Texto                                   |                           |
| Filipino (Filipinas)             | `fil-PH`| Texto                                   |                           |
| Finlandês (Finlândia)                  | `fi-FI` | Texto                                   |     Sim                      |
| Francês (Canadá)                    | `fr-CA` | Áudio (20201015)<br>Texto<br>Pronúncia|     Sim                      |
| Francês (França)                    | `fr-FR` | Áudio (20201015)<br>Texto<br>Pronúncia|      Sim                     |
| Francês (Suíça)               | `fr-CH` | Texto<br>Pronúncia                  |                           |
| Alemão (Áustria)                   | `de-AT` | Texto<br>Pronúncia                  |                           |
| Alemão (Alemanha)                   | `de-DE` | Áudio (20190701, 20200619, 20201127)<br>Texto<br>Pronúncia|  Sim                         |
| Grego (Grécia)                     | `el-GR` | Texto                                   |  Sim                         |
| Guzerate (Índia)                  | `gu-IN` | Texto                                   |                           |
| Híndi (Índia)                      | `hi-IN` | Áudio (20200701)<br>Texto                 |     Sim                      |
| Húngaro (Hungria)                | `hu-HU` | Texto                                   |                           |
| Indonésio (Indonésia)             | `id-ID` | Texto                                   |                           |
| Irlandês (Irlanda)                     | `ga-IE` | Texto                                   |                           |
| Italiano (Itália)                    | `it-IT` | Áudio (20201016)<br>Texto<br>Pronúncia|      Sim                     |
| Japonês (Japão)                   | `ja-JP` | Texto                                   |      Sim                     |
| Coreano (Coreia do Sul)                     | `ko-KR` | Áudio (20201015)<br>Texto                 |      Sim                     |
| Letão (Letônia)                   | `lv-LV` | Texto                                   |                           |
| Lituano (Lituânia)             | `lt-LT` | Texto                                   |                           |
| Malaio (Malásia)                   | `ms-MY` | Texto                                   |                           |
| Maltês (Malta)                    | `mt-MT` | Texto                                   |                           |
| Marati (Índia)                    | `mr-IN` | Texto                                   |                           |
| Norueguês, (Bokmål, Noruega)         | `nb-NO` | Texto                                   |     Sim                      |
| Polonês (Polônia)                    | `pl-PL` | Texto                                   |       Sim                    |
| Português (Brasil)                | `pt-BR` | Áudio (20190620, 20201015)<br>Texto<br>Pronúncia|          Sim                 |
| Português (Portugal)              | `pt-PT` | Texto<br>Pronúncia                  |             Sim              |
| Romeno (Romênia)                 | `ro-RO` | Texto                                   |  Sim                         |
| Russo (Rússia)                   | `ru-RU` | Áudio (20200907)<br>Texto                 |                Sim           |
| Eslovaco (Eslováquia)                  | `sk-SK` | Texto                                   |                           |
| Esloveno (Eslovênia)               | `sl-SI` | Texto                                   |                           |
| Espanhol (Argentina)                | `es-AR` | Texto<br>Pronúncia                  |                           |
| Espanhol (Bolívia)                  | `es-BO` | Texto<br>Pronúncia                  |                           |
| Espanhol (Chile)                    | `es-CL` | Texto<br>Pronúncia                  |                           |
| Espanhol (Colômbia)                 | `es-CO` | Texto<br>Pronúncia                  |                           |
| Espanhol (Costa Rica)               | `es-CR` | Texto<br>Pronúncia                  |                           |
| Espanhol (Cuba)                     | `es-CU` | Texto<br>Pronúncia                  |                           |
| Espanhol (República Dominicana)       | `es-DO` | Texto<br>Pronúncia                  |                           |
| Espanhol (Equador)                  | `es-EC` | Texto<br>Pronúncia                  |                           |
| Espanhol (El Salvador)              | `es-SV` | Texto<br>Pronúncia                  |                           |
| Espanhol (Guiné Equatorial)        | `es-GQ` | Texto                                   |                           |
| Espanhol (Guatemala)                | `es-GT` | Texto<br>Pronúncia                  |                           |
| Espanhol (Honduras)                 | `es-HN` | Texto<br>Pronúncia                  |                           |
| Espanhol (México)                   | `es-MX` | Áudio (20200907)<br>Texto<br>Pronúncia|    Sim                       |
| Espanhol (Nicarágua)                | `es-NI` | Texto<br>Pronúncia                  |                           |
| Espanhol (Panamá)                   | `es-PA` | Texto<br>Pronúncia                  |                           |
| Espanhol (Paraguai)                 | `es-PY` | Texto<br>Pronúncia                  |                           |
| Espanhol (Peru)                     | `es-PE` | Texto<br>Pronúncia                  |                           |
| Espanhol (Porto Rico)              | `es-PR` | Texto<br>Pronúncia                  |                           |
| Espanhol (Espanha)                    | `es-ES` | Áudio (20201015)<br>Texto<br>Pronúncia|  Sim                         |
| Espanhol (Uruguai)                  | `es-UY` | Texto<br>Pronúncia                  |                           |
| Espanhol (EUA)                      | `es-US` | Texto<br>Pronúncia                  |                           |
| Espanhol (Venezuela)                | `es-VE` | Texto<br>Pronúncia                  |                           |
| Sueco (Suécia)                   | `sv-SE` | Texto                                   |   Sim                        |
| Tâmil (Índia)                      | `ta-IN` | Texto                                   |                           |
| Télugo (Índia)                     | `te-IN` | Texto                                   |                           |
| Tailandês (Tailândia)                    | `th-TH` | Texto                                   |      Sim                     |
| Turco (Turquia)                   | `tr-TR` | Texto                                   |                           |
| Vietnamita (Vietnã)               | `vi-VN` | Texto                                   |                           |

## <a name="text-to-speech"></a>Conversão de texto em fala

O SDK do Microsoft Speech e as APIs REST são compatíveis com essas vozes, cada uma delas compatível com um idioma e um dialeto específicos, identificados pela localidade. Você também pode obter uma lista completa de idiomas e vozes compatíveis para cada região/ponto de extremidade específico por meio da [API vozes/lista](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

Vozes neurais podem ser usadas para interagir com chatbots e assistentes de voz de maneira mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros e aprimorar sistemas de navegação veiculares. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

> [!NOTE]
> As vozes neurais são criadas com base em amostras que usam uma taxa de amostragem de 24 kHz.
> Em todas as vozes, é possível aumentar ou reduzir para outras taxas de amostragem durante a sintetização.


| Idioma | Local | Gênero | Nome da voz | Suporte de estilo |
|---|---|---|---|---|
| Árabe (Egito) | `ar-EG` | Feminino | `ar-EG-SalmaNeural` | Geral |
| Árabe (Egito) | `ar-EG` | Masculino | `ar-EG-ShakirNeural` | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Feminino | `ar-SA-ZariyahNeural` | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Masculino | `ar-SA-HamedNeural` | Geral |
| Búlgaro (Bulgária) | `bg-BG` | Feminino | `bg-BG-KalinaNeural` | Geral |
| Búlgaro (Bulgária) | `bg-BG` | Masculino | `bg-BG-BorislavNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Feminino | `ca-ES-AlbaNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Feminino | `ca-ES-JoanaNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Masculino | `ca-ES-EnricNeural` | Geral |
| Chinês (Cantonês tradicional) | `zh-HK` | Feminino | `zh-HK-HiuGaaiNeural` | Geral |
| Chinês (Cantonês tradicional) | `zh-HK` | Feminino | `zh-HK-HiuMaanNeural` | Geral |
| Chinês (Cantonês tradicional) | `zh-HK` | Masculino | `zh-HK-WanLungNeural` | Geral |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoxiaoNeural` | Geral, vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoyouNeural` | Voz de criança, otimização para narração de histórias |
| Chinês (mandarim, simplificado) | `zh-CN` | Masculino | `zh-CN-YunyangNeural` | Otimizado para a leitura de notícias,<br /> com vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Masculino | `zh-CN-YunyeNeural` | Otimização para narração de histórias |
| Chinês (Mandarim Taiwanês) | `zh-TW` | Feminino | `zh-TW-HsiaoChenNeural` | Geral |
| Chinês (Mandarim Taiwanês) | `zh-TW` | Feminino | `zh-TW-HsiaoYuNeural` | Geral |
| Chinês (Mandarim Taiwanês) | `zh-TW` | Masculino | `zh-TW-YunJheNeural` | Geral |
| Croata (Croácia) | `hr-HR` | Feminino | `hr-HR-GabrijelaNeural` | Geral |
| Croata (Croácia) | `hr-HR` | Masculino | `hr-HR-SreckoNeural` | Geral |
| Tcheco (República Tcheca) | `cs-CZ` | Feminino | `cs-CZ-VlastaNeural` | Geral |
| Tcheco (República Tcheca) | `cs-CZ` | Masculino | `cs-CZ-AntoninNeural` | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Feminino | `da-DK-ChristelNeural` | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Masculino | `da-DK-JeppeNeural` | Geral |
| Holandês (Bélgica) | `nl-BE` | Feminino | `nl-BE-DenaNeural` <sup>Novo</sup> | Geral | 
| Holandês (Bélgica) | `nl-BE` | Masculino | `nl-BE-ArnaudNeural` <sup>Novo</sup> | Geral | 
| Holandês (Países Baixos) | `nl-NL` | Feminino | `nl-NL-ColetteNeural` | Geral |
| Holandês (Países Baixos) | `nl-NL` | Feminino | `nl-NL-FennaNeural` | Geral |
| Holandês (Países Baixos) | `nl-NL` | Masculino | `nl-NL-MaartenNeural` | Geral |
| Inglês (Austrália) | `en-AU` | Feminino | `en-AU-NatashaNeural` | Geral |
| Inglês (Austrália) | `en-AU` | Masculino | `en-AU-WilliamNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Feminino | `en-CA-ClaraNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Masculino | `en-CA-LiamNeural` | Geral |
| Inglês (Índia) | `en-IN` | Feminino | `en-IN-NeerjaNeural` | Geral |
| Inglês (Índia) | `en-IN` | Masculino | `en-IN-PrabhatNeural` | Geral |
| Inglês (Irlanda) | `en-IE` | Feminino | `en-IE-EmilyNeural` | Geral |
| Inglês (Irlanda) | `en-IE` | Masculino | `en-IE-ConnorNeural` | Geral |
| Inglês (Filipinas) | `en-PH` | Feminino | `en-PH-RosaNeural` <sup>Novo</sup> | Geral | 
| Inglês (Filipinas) | `en-PH` | Masculino | `en-PH-JamesNeural` <sup>Novo</sup> | Geral | 
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-LibbyNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Feminino | `en-GB-MiaNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Masculino | `en-GB-RyanNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Feminino | `en-US-AriaNeural` | Geral, vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Inglês (Estados Unidos) | `en-US` | Feminino | `en-US-JennyNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Masculino | `en-US-GuyNeural` | Geral |
| Estoniano (Estônia) | `et-EE` | Feminino | `et-EE-AnuNeural` | Geral |
| Estoniano (Estônia) | `et-EE` | Masculino | `et-EE-KertNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Feminino | `fi-FI-NooraNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Feminino | `fi-FI-SelmaNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Masculino | `fi-FI-HarriNeural` | Geral |
| Francês (Bélgica) | `fr-BE` | Feminino | `fr-BE-CharlineNeural` <sup>Novo</sup> | Geral | 
| Francês (Bélgica) | `fr-BE` | Masculino | `fr-BE-GerardNeural` <sup>Novo</sup> | Geral | 
| Francês (Canadá) | `fr-CA` | Feminino | `fr-CA-SylvieNeural` | Geral |
| Francês (Canadá) | `fr-CA` | Masculino | `fr-CA-AntoineNeural` | Geral |
| Francês (Canadá) | `fr-CA` | Masculino | `fr-CA-JeanNeural` | Geral |
| Francês (França) | `fr-FR` | Feminino | `fr-FR-DeniseNeural` | Geral |
| Francês (França) | `fr-FR` | Masculino | `fr-FR-HenriNeural` | Geral |
| Francês (Suíça) | `fr-CH` | Feminino | `fr-CH-ArianeNeural` | Geral |
| Francês (Suíça) | `fr-CH` | Masculino | `fr-CH-FabriceNeural` | Geral |
| Alemão (Áustria) | `de-AT` | Feminino | `de-AT-IngridNeural` | Geral |
| Alemão (Áustria) | `de-AT` | Masculino | `de-AT-JonasNeural` | Geral |
| Alemão (Alemanha) | `de-DE` | Feminino | `de-DE-KatjaNeural` | Geral |
| Alemão (Alemanha) | `de-DE` | Masculino | `de-DE-ConradNeural` | Geral |
| Alemão (Suíça) | `de-CH` | Feminino | `de-CH-LeniNeural` | Geral |
| Alemão (Suíça) | `de-CH` | Masculino | `de-CH-JanNeural` | Geral |
| Grego (Grécia) | `el-GR` | Feminino | `el-GR-AthinaNeural` | Geral |
| Grego (Grécia) | `el-GR` | Masculino | `el-GR-NestorasNeural` | Geral |
| Hebraico (Israel) | `he-IL` | Feminino | `he-IL-HilaNeural` | Geral |
| Hebraico (Israel) | `he-IL` | Masculino | `he-IL-AvriNeural` | Geral |
| Híndi (Índia) | `hi-IN` | Feminino | `hi-IN-SwaraNeural` | Geral |
| Híndi (Índia) | `hi-IN` | Masculino | `hi-IN-MadhurNeural` | Geral |
| Húngaro (Hungria) | `hu-HU` | Feminino | `hu-HU-NoemiNeural` | Geral |
| Húngaro (Hungria) | `hu-HU` | Masculino | `hu-HU-TamasNeural` | Geral |
| Indonésio (Indonésia) | `id-ID` | Feminino | `id-ID-GadisNeural` | Geral |
| Indonésio (Indonésia) | `id-ID` | Masculino | `id-ID-ArdiNeural` | Geral |
| Irlandês (Irlanda) | `ga-IE` | Feminino | `ga-IE-OrlaNeural` | Geral |
| Irlandês (Irlanda) | `ga-IE` | Masculino | `ga-IE-ColmNeural` | Geral |
| Italiano (Itália) | `it-IT` | Feminino | `it-IT-ElsaNeural` | Geral |
| Italiano (Itália) | `it-IT` | Feminino | `it-IT-IsabellaNeural` | Geral |
| Italiano (Itália) | `it-IT` | Masculino | `it-IT-DiegoNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Feminino | `ja-JP-NanamiNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Masculino | `ja-JP-KeitaNeural` | Geral |
| Coreano (Coreia do Sul) | `ko-KR` | Feminino | `ko-KR-SunHiNeural` | Geral |
| Coreano (Coreia do Sul) | `ko-KR` | Masculino | `ko-KR-InJoonNeural` | Geral |
| Letão (Letônia) | `lv-LV` | Feminino | `lv-LV-EveritaNeural` | Geral |
| Letão (Letônia) | `lv-LV` | Masculino | `lv-LV-NilsNeural` | Geral |
| Lituano (Lituânia) | `lt-LT` | Feminino | `lt-LT-OnaNeural` | Geral |
| Lituano (Lituânia) | `lt-LT` | Masculino | `lt-LT-LeonasNeural` | Geral |
| Malaio (Malásia) | `ms-MY` | Feminino | `ms-MY-YasminNeural` | Geral |
| Malaio (Malásia) | `ms-MY` | Masculino | `ms-MY-OsmanNeural` | Geral |
| Maltês (Malta) | `mt-MT` | Feminino | `mt-MT-GraceNeural` | Geral |
| Maltês (Malta) | `mt-MT` | Masculino | `mt-MT-JosephNeural` | Geral |
| Norueguês, (Bokmål, Noruega) | `nb-NO` | Feminino | `nb-NO-IselinNeural` | Geral |
| Norueguês, (Bokmål, Noruega) | `nb-NO` | Feminino | `nb-NO-PernilleNeural` | Geral |
| Norueguês, (Bokmål, Noruega) | `nb-NO` | Masculino | `nb-NO-FinnNeural` | Geral |
| Polonês (Polônia) | `pl-PL` | Feminino | `pl-PL-AgnieszkaNeural` | Geral |
| Polonês (Polônia) | `pl-PL` | Feminino | `pl-PL-ZofiaNeural` | Geral |
| Polonês (Polônia) | `pl-PL` | Masculino | `pl-PL-MarekNeural` | Geral |
| Português (Brasil) | `pt-BR` | Feminino | `pt-BR-FranciscaNeural` | Geral, vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Português (Brasil) | `pt-BR` | Masculino | `pt-BR-AntonioNeural` | Geral |
| Português (Portugal) | `pt-PT` | Feminino | `pt-PT-FernandaNeural` | Geral |
| Português (Portugal) | `pt-PT` | Feminino | `pt-PT-RaquelNeural` | Geral |
| Português (Portugal) | `pt-PT` | Masculino | `pt-PT-DuarteNeural` | Geral |
| Romeno (Romênia) | `ro-RO` | Feminino | `ro-RO-AlinaNeural` | Geral |
| Romeno (Romênia) | `ro-RO` | Masculino | `ro-RO-EmilNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Feminino | `ru-RU-DariyaNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Feminino | `ru-RU-SvetlanaNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Masculino | `ru-RU-DmitryNeural` | Geral |
| Eslovaco (Eslováquia) | `sk-SK` | Feminino | `sk-SK-ViktoriaNeural` | Geral |
| Eslovaco (Eslováquia) | `sk-SK` | Masculino | `sk-SK-LukasNeural` | Geral |
| Esloveno (Eslovênia) | `sl-SI` | Feminino | `sl-SI-PetraNeural` | Geral |
| Esloveno (Eslovênia) | `sl-SI` | Masculino | `sl-SI-RokNeural` | Geral |
| Espanhol (México) | `es-MX` | Feminino | `es-MX-DaliaNeural` | Geral |
| Espanhol (México) | `es-MX` | Masculino | `es-MX-JorgeNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Feminino | `es-ES-ElviraNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Masculino | `es-ES-AlvaroNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Feminino | `sv-SE-HilleviNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Feminino | `sv-SE-SofieNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Masculino | `sv-SE-MattiasNeural` | Geral |
| Tâmil (Índia) | `ta-IN` | Feminino | `ta-IN-PallaviNeural` | Geral |
| Tâmil (Índia) | `ta-IN` | Masculino | `ta-IN-ValluvarNeural` | Geral |
| Télugo (Índia) | `te-IN` | Feminino | `te-IN-ShrutiNeural` | Geral |
| Télugo (Índia) | `te-IN` | Masculino | `te-IN-MohanNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Feminino | `th-TH-AcharaNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Feminino | `th-TH-PremwadeeNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Masculino | `th-TH-NiwatNeural` | Geral |
| Turco (Turquia) | `tr-TR` | Feminino | `tr-TR-EmelNeural` | Geral |
| Turco (Turquia) | `tr-TR` | Masculino | `tr-TR-AhmetNeural` | Geral |
| Ucraniano (Ucrânia) | `uk-UA` | Feminino | `uk-UA-PolinaNeural` <sup>Novo</sup> | Geral | 
| Ucraniano (Ucrânia) | `uk-UA` | Masculino | `uk-UA-OstapNeural` <sup>Novo</sup> | Geral | 
| Urdu (Paquistão) | `ur-PK` | Feminino | `ur-PK-UzmaNeural` <sup>Novo</sup>  | Geral | 
| Urdu (Paquistão) | `ur-PK` | Masculino | `ur-PK-AsadNeural` <sup>Novo</sup> | Geral | 
| Vietnamita (Vietnã) | `vi-VN` | Feminino | `vi-VN-HoaiMyNeural` | Geral |
| Vietnamita (Vietnã) | `vi-VN` | Masculino | `vi-VN-NamMinhNeural` | Geral |
| Galês (Reino Unido) | `cy-GB` | Feminino | `cy-GB-NiaNeural` <sup>Novo</sup> | Geral | 
| Galês (Reino Unido) | `cy-GB` | Masculino | `cy-GB-AledNeural` <sup>Novo</sup> | Geral | 

#### <a name="neural-voices-in-preview"></a>Vozes neurais em versão prévia

As vozes neurais a seguir estão em versão prévia pública. 

| Idioma                         | Local  | Gênero | Nome da voz                             | Suporte de estilo |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaohanNeural` | Geral, vários estilos disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaomoNeural` | Geral, vários estilos e encenações disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoruiNeural` | Voz de pessoa mais velha, vários estilos disponíveis [usando o SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Feminino | `zh-CN-XiaoxuanNeural` | Geral, vários estilos e encenações disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (mandarim, simplificado) | `zh-CN` | Masculino   | `zh-CN-YunxiNeural` | Geral, vários estilos disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |

> [!IMPORTANT]
> As vozes em versão prévia pública estão disponíveis somente em três regiões de serviço: Leste dos EUA, Oeste da Europa e Sudeste da Ásia.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#neural-and-standard-voices).

Para saber como configurar e ajustar as vozes neurais, como os Estilos de Fala, confira [Linguagem de Marcação de Sintetização de Voz](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> A voz `en-US-JessaNeural` foi alterada para `en-US-AriaNeural`. Se você usava a voz "Jessa", faça a conversão para "Aria".

> [!TIP]
> Você poderá continuar a usar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#neural-and-standard-voices).

> [!NOTE]
> As vozes padrão são criadas com base em amostras que usam uma taxa de amostragem de 16 kHz, com duas exceções.
> As vozes **en-US-AriaRUS** e **en-US-GuyRUS** também são criadas com base em amostras com uma taxa de amostragem de 24 kHz.
> Em todas as vozes, é possível aumentar ou reduzir para outras taxas de amostragem durante a sintetização.

| Idioma | Localidade (BCP-47) | Gênero | Nome da voz |
|--|--|--|--|
| Árabe (Árabe) | `ar-EG` | Feminino | `ar-EG-Hoda`|
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

A Voz Personalizada está disponível na camada padrão e na neural. Os idiomas compatíveis são diferentes nessas duas camadas. 

| Idioma | Local | Standard | Neural |
|--|--|--|--|
| Chinês (mandarim, simplificado) | `zh-CN` | Sim | Sim |
| Chinês (mandarim, simplificado), inglês bilíngue | `zh-CN` bilíngue | Sim | Sim |
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
> Não damos suporte ao treinamento de modelo bilíngue em Voz Personalizada, exceto para o modelo bilíngue chinês-inglês. Selecione "Modelo bilíngue chinês/inglês" se você deseja treinar uma voz chinesa que também pode falar em inglês. O treinamento do modelo bilíngue chinês-inglês que usa o método padrão está disponível somente no Norte da Europa e no Centro-Norte dos EUA. O treinamento de Sintetização de Voz Personalizada está disponível no Sul do Reino Unido e no Leste dos EUA.

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
|Chinês (mandarim, simplificado) | zh-CN     |     n/d |     sim |     sim|
|Inglês (Austrália)     | en-AU     | n/d     | sim     | sim|
|Inglês (Canadá)     | en-CA     | n/d |     sim |     sim|
|Inglês (Reino Unido)     | en-GB     | n/d     | sim     | sim|
|Francês (Canadá)     | fr-CA     | n/d     | sim |     sim|
|Francês (França)     | fr-FR     | n/d     | sim     | sim|
|Alemão (Alemanha)     | de-DE     | n/d     | sim     | sim|
|Italiano | it-IT     |     n/d     | sim |     sim|
|Japonês     | ja-JP | n/d     | sim     | sim|
|Português (Brasil) | pt-BR |     n/d |     sim |     sim|
|Espanhol (México)     | es-MX     | n/d |     sim |     sim|
|Espanhol (Espanha)     | es-ES | n/d     | sim |     sim|

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta gratuita do Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer fala em C#](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
