---
title: Suporte a idiomas – pesquisa Visual computacional
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas com suporte a recursos de pesquisa Visual computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759598"
---
# <a name="language-support-for-computer-vision"></a>Suporte ao idioma para pesquisa Visual computacional

Alguns recursos de pesquisa Visual computacional suportam a vários idiomas; todos os recursos não mencionados aqui só há suporte para inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

Pesquisa Visual computacional pode reconhecer texto em vários idiomas. Especificamente, o [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API dá suporte a uma variedade de linguagens, enquanto o [leitura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API e [reconhecer texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API oferecem suporte apenas em inglês. Ver [reconhecer texto impresso e manuscrito](concept-recognizing-text.md) para obter mais informações sobre essa funcionalidade e as vantagens de cada API.

OCR automaticamente detecta o idioma de material de entrada, portanto, não há necessidade de especificar um código de idioma na chamada à API. No entanto, os códigos de idioma são sempre retornados como o valor da `"language"` nó na resposta JSON.

|Linguagem| Código de idioma | API DE OCR |
|:-----|:----:|:-----:|
|Árabe | `ar`|✔ |
|Chinês (Simplificado) | `zh-Hans`|✔ |
|Chinês (Tradicional) | `zh-Hant`|✔ |
|Tcheco | `cs` |✔ |
|Dinamarquês | `da` |✔ |
|Holandês | `nl` |✔ |
|Inglês | `en` |✔ |
|Finlandês | `fi` |✔ |
|Francês | `fr` |✔ |
|Alemão | `de` |✔ |
|Grego | `el` |✔ |
|Húngaro | `hu` |✔ |
|Italiano | `it` |✔ |
|Japonês | `ja` |✔ |
|Coreano | `ko` |✔ |
|Norueguês | `nb` |✔ |
|Polonês | `pl` |✔ |
|Português | `pt` |✔ |
|Romeno | `ro` |✔ |
|Russo | `ru` |✔ |
|Sérvio (cirílico) | `sr-Cyrl` |✔ |
|Sérvio (latino) | `sr-Latn` |✔ |
|Eslovaco | `sk` |✔ |
|Espanhol | `es` |✔ |
|Sueco | `sw` |✔ |
|Turco | `tr` |✔ |

## <a name="image-analysis"></a>Análise de imagem

Algumas ações do [analisar – imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API pode retornar resultados em outras linguagens, especificadas com o `language` parâmetro de consulta. Outras ações retornam resultados em inglês, independentemente de qual idioma for especificado, e outras pessoas lançar uma exceção para idiomas sem suporte. Ações são especificadas com o `visualFeatures` e `details` parâmetros de consulta, consulte o [visão geral](home.md) para obter uma lista de todas as ações que você pode fazer com a análise de imagem.

|Linguagem | Código de idioma | Categories | Marcas | DESCRIÇÃO | Adulto | Marcas | Cor | Faces | ImageType | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglês | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Próximas etapas

Começar a usar os recursos de pesquisa Visual computacional mencionados neste guia.

* [Analisar uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrair texto impresso (REST)](./quickstarts/csharp-print-text.md)