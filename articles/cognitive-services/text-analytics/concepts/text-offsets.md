---
title: Deslocamentos de texto na API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os deslocamentos causados por codificações multilíngues e emojis.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: f5b63503792b13e089568004ba67e5be8a3d0c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932364"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Deslocamentos de texto na saída de API de Análise de Texto

O suporte multilíngue e Emoji levou a codificações Unicode que usam mais de um [ponto de código](https://wikipedia.org/wiki/Code_point) para representar um único caractere exibido, chamado de grafemas. Por exemplo, emojis como 🌷 e 👍 podem usar vários caracteres para compor a forma com caracteres adicionais para atributos visuais, como o tom de capa. Da mesma forma, a palavra híndi `अनुच्छेद` é codificada como cinco letras e três marcas de combinação.

Devido aos diferentes comprimentos de codificações multilíngues e de emojis, o API de Análise de Texto pode retornar deslocamentos na resposta.

## <a name="offsets-in-the-api-response"></a>Deslocamentos na resposta da API. 

Sempre que os deslocamentos forem retornados à resposta da API, como [reconhecimento de entidade nomeada](../how-tos/text-analytics-how-to-entity-linking.md) ou [análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md), lembre-se do seguinte:

* Os elementos na resposta podem ser específicos para o ponto de extremidade que foi chamado. 
* As cargas HTTP POST/GET são codificadas em [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), que podem ou não ser a codificação de caracteres padrão no seu compilador do lado do cliente ou sistema operacional.
* Os deslocamentos se referem a contagens de grafemas com base no padrão [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) , não em contagens de caracteres.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extraindo subcadeias de texto com deslocamentos

Os deslocamentos podem causar problemas ao usar métodos de subcadeias de caracteres baseados em caractere, por exemplo, o método de [subcadeias](/dotnet/api/system.string.substring) de caracteres .net (). Um problema é que um deslocamento pode fazer com que um método de subcadeia de caracteres termine no meio de uma codificação grafemas de vários caracteres, em vez do final.

No .NET, considere usar a classe [StringInfo](/dotnet/api/system.globalization.stringinfo) , que permite que você trabalhe com uma cadeia de caracteres como uma série de elementos textuais, em vez de objetos de caractere individuais. Você também pode procurar por bibliotecas de divisores grafemas em seu ambiente de software preferido. 

O API de Análise de Texto também retorna esses elementos textuais, por conveniência.

## <a name="offsets-in-api-version-31-preview"></a>Deslocamentos na versão de API 3,1-Preview

A partir da versão de API 3,1-Preview. 1, todos os API de Análise de Texto pontos de extremidade que retornam um deslocamento oferecerão suporte ao `stringIndexType` parâmetro. Esse parâmetro ajusta os `offset` atributos e `length` na saída da API para corresponder ao esquema de iteração de cadeia de caracteres solicitado. Atualmente, há suporte para três tipos:

1. `textElement_v8`(padrão): itera sobre graphemes conforme definido pelo padrão [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)
2. `unicodeCodePoint`: itera sobre [pontos de código Unicode](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), o esquema padrão para Python 3
3. `utf16CodeUnit`: itera em [unidades de código UTF-16](https://unicode.org/faq/utf_bom.html#UTF16), o esquema padrão para JavaScript, Java e .net

Se o `stringIndexType` solicitado corresponder ao ambiente de programação de sua escolha, a extração de subcadeia de caracteres poderá ser feita usando métodos padrão substring ou Slice. 

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detectar o idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
