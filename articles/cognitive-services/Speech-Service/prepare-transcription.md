---
title: Diretrizes de transcrição para treinamento de modelos de serviços de fala
titleSuffix: Azure Cognitive Services
description: Saiba como preparar o texto para personalizar acústicos e modelos de linguagem e vozes para os serviços de fala.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 0d7508ed9cf1807fa05c57a1d60c804af7d2244f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461435"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Diretrizes de transcrição para usar o serviço de fala

Para personalizar a **Conversão de Fala em Texto** ou a **Conversão de Texto em Fala**, você deverá fornecer o texto junto com a fala. Cada linha no texto corresponde a uma única declaração. O texto deve corresponder a fala mais próximo possível. O texto é chamado de *transcrição* e você deverá criá-lo em um formato específico.

Os serviços de fala normalizar a entrada para manter o texto consistente.

Este artigo descreve os dois tipos de normalização. As diretrizes variam ligeiramente para vários idiomas.

## <a name="us-english-en-us"></a>Inglês dos EUA (en-us)

Os dados de texto devem ser gravados um enunciado por linha, em texto sem formatação, usando apenas o conjunto de caracteres ASCII.

Evite o uso de caracteres de pontuação estendidos (Latin-1) ou Unicode. Esses caracteres podem ser incluídos inadvertidamente quando você prepara os dados em um programa de processamento de texto ou copia dados de páginas da Web. Substitua os caracteres por substituições ASCII apropriadas. Por exemplo: 

| Caracteres a evitar | Substituição |
|----- | ----- |
| "Hello world" (de abertura e fechamento de aspas duplas) | "Hello world" (aspas duplas) |
| John’s day (aspa simples à direita) | John's day (apóstrofo) |
| it was good—no, it was great! (travessão) | it was good--no, it was great! (hifens) |

### <a name="text-normalization-rules-for-english"></a>Regras de normalização de texto para inglês

Os serviços de fala realizar as seguintes regras de normalização:

* Usando letras minúsculas para todo o texto
* Remover toda a pontuação exceto apóstrofos internos da palavra
* Expandir os números para formato falado, incluindo quantias em dinheiro

Estes são alguns exemplos:

| Texto original | Após a normalização |
|----- | ----- |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

Aplique a seguinte normalização às suas transcrições de texto:

* Abreviaturas devem ser escritas em palavras.
* Cadeias numéricas não padrão (como algumas datas ou formulários contábeis) devem ser escritas em palavras.
* Palavras com caracteres não alfabéticos ou caracteres alfanuméricos mistos devem ser transcritas como pronunciadas.
* Deixe as abreviações que são pronunciadas como palavras intocadas (por exemplo, "radar", "laser", "RAM" ou "NATO").
* Escreva abreviações que são pronunciadas como letras separadas, com letras separadas por espaços (por exemplo, "IBM", "CPU", "FBI", "TBD" ou "NaN").

Estes são alguns exemplos:

| Texto original | Após a normalização |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>Chinês (zh-cn)

Dados de texto que são carregados para os serviços de fala personalizado devem usar a codificação UTF-8 com um marcador de ordem de byte. O arquivo deve ser gravado com um enunciado por linha.

Evite o uso de caracteres de pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente quando você prepara os dados em um programa de processamento de texto ou copia dados de páginas da Web. Substitua-os pelos correspondentes apropriados de largura total. Por exemplo: 

| Caracteres a evitar | Substituição |
|----- | ----- |
| "你好" (de abertura e fechamento de aspas duplas) | "你好" (aspas duplas) |
| 需要什么帮助? (ponto de interrogação) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Regras de normalização de texto para chinês

Os serviços de fala realizar as seguintes regras de normalização:

* Remover toda a pontuação
* Expandindo números para a forma falada
* Convertendo letras de largura total para letras de meia largura
* Usando letras maiusculas para todas as palavras em inglês

Estes são alguns exemplos:

| Texto original | Após a normalização |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Antes de importar seu texto, aplique a seguinte normalização a ele:

* Abreviações devem ser escritas em palavras (como no formato falado).
* Grave cadeia de caracteres numéricas na forma falada.

Estes são alguns exemplos:

| Texto original | Após a normalização |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Outras linguagens

Os dados de texto enviados para o serviço **Speech to Text** devem usar a codificação UTF-8 com um marcador de ordem de bytes. O arquivo deve ser gravado com um enunciado por linha.

> [!NOTE]
> Os exemplos a seguir usam o alemão. No entanto, as diretrizes se aplicam a todos os idiomas que não são o inglês dos EUA ou o chinês.

### <a name="text-normalization-rules-for-german"></a>Regras de normalização de texto para alemão

Os serviços de fala realizar as seguintes regras de normalização:

* Usando letras minúsculas para todo o texto
* Remover todas as pontuações, incluindo vários tipos de aspas ("teste", "teste", "teste" e "teste" estão corretos)
* Descartando linhas com qualquer caractere especial do conjunto  ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
* Expandir os números para forma de palavra, incluindo dólar ou valores de Euro
* Aceitando tremas somente para um, o e você. outras pessoas serão substituídas por "th" ou descartadas

Estes são alguns exemplos:

| Texto original | Após a normalização |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

Antes de importar seu texto, aplique a seguinte normalização a ele:

* Pontos decimais devem ser "," e não ".".
* Os separadores de hora entre as horas e os minutos devem ser ":" e não "". (por exemplo, 12:00 Uhr).
* Abreviações como "ca". não são substituídas. É recomendável que você use a forma completa.
* Os quatro operadores matemáticos principais (+, -, \*, e /) são removidos. É recomendável substituí-los com sua forma literal: "Além disso," "menos," "sites perigosos" e "geteilt".
* A mesma regra se aplica aos operadores de comparação (=, <, e >). É recomendável substituí-los por "gleich", "kleiner als," e "als grösser".
* Use frações, como 3 e 4, na forma de palavra (por exemplo, "drei viertel" em vez de ¾).
* Substituir o símbolo de € com a forma de palavra "Euro".

Estes são alguns exemplos:

| Texto original | Após a normalização do usuário | Após a normalização do sistema |
|--------  | ----- | -------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Próximas etapas

- [Obter sua assinatura de avaliação de Serviços de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
