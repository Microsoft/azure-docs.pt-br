---
title: Diretrizes de transcrições rotuladas por pessoas – serviço de fala
titleSuffix: Azure Cognitive Services
description: Se você estiver procurando melhorar a precisão do reconhecimento, especialmente os problemas causados quando palavras são excluídas ou substituídas incorretamente, você desejará usar transcrições com rótulo humano junto com seus dados de áudio. O que são transcrições com rótulo humano? É fácil, eles são palavras-por-palavra, transcrições textuais de um arquivo de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1645e97e5648032a1281e7cb410c42f0a28b6767
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559646"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Como criar transcrições com rótulo humano

Se você estiver procurando melhorar a precisão do reconhecimento, especialmente os problemas causados quando palavras são excluídas ou substituídas incorretamente, você desejará usar transcrições com rótulo humano junto com seus dados de áudio. O que são transcrições com rótulo humano? É fácil, eles são palavras-por-palavra, transcrições textuais de um arquivo de áudio.

Um exemplo grande de dados de transcrição é necessário para melhorar o reconhecimento, sugerimos fornecer entre 10 e 1.000 horas de dados de transcrição. Nesta página, examinaremos as diretrizes criadas para ajudá-lo a criar transcrições de alta qualidade. Este guia é dividido por localidade, com seções para inglês americano, mandarim chinês e alemão.

## <a name="us-english-en-us"></a>Inglês dos EUA (en-US)

As transcrições com rótulo humano para áudio em inglês devem ser fornecidas como texto sem formatação, apenas usando caracteres ASCII. Evite o uso de caracteres de Pontuação latino-1 ou Unicode. Esses caracteres são frequentemente adicionados inadvertidamente ao copiar texto de um aplicativo de processamento de textos ou recorte de dados de páginas da Web. Se esses caracteres estiverem presentes, certifique-se de atualizá-los com a substituição ASCII apropriada.

Veja alguns exemplos:

| Caracteres a evitar | Substituição | Observações |
|---------------------|--------------|-------|
| "Olá, mundo" | "Olá, Mundo" | As marcas de aspas de abertura e fechamento foram substituídas por caracteres ASCII apropriados. |
| Dia de João | Dia de João | O apóstrofo foi substituído pelo caractere ASCII apropriado. |
| it was good—no, it was great! | it was good--no, it was great! | O travessão foi substituído por dois hifens. |

### <a name="text-normalization-for-us-english"></a>Normalização de texto para inglês americano

A normalização de texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente. no entanto, é recomendável usar essas diretrizes ao preparar seus dados de transcrição rotulados por pessoas:

* Escreva abreviações em palavras.
* Escreva cadeias de caracteres numéricas não padrão em palavras (como termos de contabilidade).
* Caracteres não alfabéticos ou caracteres alfanuméricos mistos devem ser transcritas como pronunciados.
* Abreviações pronunciadas como palavras não devem ser editadas (como "radar", "laser", "RAM" ou "OTAN").
* Escreva abreviações que são pronunciadas como letras separadas com cada letra separada por um espaço.

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| Meu tipo de sangue é O + | My blood type is O positive |
| Água é H20 | Água é H 2 O |
| Reproduzir OU812 por Van Halen | Reproduzir O U 8 1 2 por Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

* Use letras minúsculas.
* Remova toda a pontuação, exceto apóstrofos dentro de palavras.
* Expanda números em palavras/forma falada, como valores de dólar.

Aqui estão alguns exemplos de normalização executadas automaticamente na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
It costs $3.14| it costs three fourteen |

## <a name="mandarin-chinese-zh-cn"></a>Mandarim chinês (ZH-CN)

As transcrições rotuladas pelo homem para áudio mandarim chinês devem ser codificadas em UTF-8 com um marcador de ordem de byte. Evite o uso de caracteres de pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente quando você prepara os dados em um programa de processamento de texto ou recorta dados de páginas da Web. Se esses caracteres estiverem presentes, certifique-se de atualizá-los com a substituição de largura inteira apropriada.

Veja alguns exemplos:

| Caracteres a evitar | Substituição | Observações |
|---------------------|--------------|-------|
| "你好" | "你好" | As marcas de aspas de abertura e fechamento foram substituídas pelos caracteres apropriados. |
| 需要什么帮助? | 需要什么帮助？ | O ponto de interrogação foi substituído pelo caractere apropriado. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalização de texto para chinês mandarim

A normalização de texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente. no entanto, é recomendável usar essas diretrizes ao preparar seus dados de transcrição rotulados por pessoas:

* Escreva abreviações em palavras.
* Grave cadeia de caracteres numéricas na forma falada.

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

* Remover toda a Pontuação
* Expandir números para formulário falado
* Converter letras de largura inteira em letras de meia largura
* Usando letras maiusculas para todas as palavras em inglês

Aqui estão alguns exemplos de normalização executadas automaticamente na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Alemão (de-DE) e outras linguagens

As transcrições rotuladas pelo homem para áudio alemão (e outras linguagens de chinês não inglês ou mandarim) devem ser codificadas em UTF-8 com um marcador de ordem de byte. Uma transcrição com rótulo humano deve ser fornecida para cada arquivo de áudio.

### <a name="text-normalization-for-german"></a>Normalização de texto para alemão

A normalização de texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas ao texto automaticamente. no entanto, é recomendável usar essas diretrizes ao preparar seus dados de transcrição rotulados por pessoas:

*   Gravar pontos decimais como "," e não ".".
*   Gravar separadores de tempo como ":" e não "." (por exemplo: 12:00 Uhr).
*   Abreviações como "ca". não são substituídas. Recomendamos que você use o formulário falado completo.
*   Os quatro operadores matemáticos principais (+, -, \*, e /) são removidos. É recomendável substituí-los pelo formato escrito: "mais", "menos," "mal" e "geteilt".
*   Operadores de comparação são removidos (=, < e >). É recomendável substituí-los por "gleich", "kleiner als," e "als grösser".
*   Escreva frações, como 3/4, no formato escrito (por exemplo: "Drei Viertel" em vez de 3/4).
*   Substitua o símbolo "€" pelo formato escrito "euro".

Aqui estão alguns exemplos de normalização que você deve executar na transcrição:

| Texto original | Texto após a normalização do usuário | Texto após a normalização do sistema |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

As seguintes regras de normalização são aplicadas automaticamente às transcrições:

* Use letras minúsculas para todo o texto.
* Remova toda a pontuação, incluindo vários tipos de aspas ("Test", "Test", "Test" e «Test» estão OK).
* Descartar linhas com qualquer caractere especial deste conjunto: ¢ ¤ ¥, ¦ § © ª ¬® ° ± ² μ × ÿize ¬ ¬.
* Expanda números para formulário falado, incluindo valores de moeda ou de euro.
* Aceite apenas tremos de a, o e você. Outras serão substituídas por "th" ou descartadas.

Aqui estão alguns exemplos de normalização executadas automaticamente na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>Próximas etapas

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
