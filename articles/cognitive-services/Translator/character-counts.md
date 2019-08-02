---
title: Contagens de caractere - API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Como a API de Tradução de Texto conta caracteres.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e3a16d9272e75f9a94f5381c1681c036d177e0f6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595995"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Como a API de Tradução de Texto conta caracteres

A API de Tradução de Texto conta cada ponto de código Unicode do texto de entrada como um caractere. Cada tradução de um texto para um idioma conta como uma tradução separada, mesmo que a solicitação tenha sido feita em uma única chamada à API que traduziu para vários idiomas. O tamanho da resposta não é importante.

O que conta é:

* O texto é transferido para a API de Tradução de Texto no corpo da solicitação
   * `Text` ao usar os métodos Tradução, Transliteração e Pesquisa no dicionário
   * `Text` e `Translation` ao usar o método Exemplos de dicionário
* Todas as marcações: HTML, marcas XML e afins no campo de texto do corpo da solicitação. A notação JSON usada para construir a solicitação (por exemplo "Text:") não é contabilizada.
* Uma letra individual
* Pontuação
* Espaço, tabulação, marcação e qualquer tipo de caractere de espaço em branco
* Cada ponto de código definido no Unicode
* Uma tradução repetida, mesmo que você já tenha traduzido o mesmo texto anteriormente

Para scripts com base em ideogramas, como chinês e japonês Kanji, a API de Tradução de Texto ainda contará o número de pontos de código Unicode, um caractere por ideograma. Exceção: Um caractere não-Unicode conta como dois caracteres.

O número de solicitações, palavras, bytes ou frases é irrelevante na contagem de caracteres.

Chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caractere. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence sejam de uma proporção razoável para o uso de outras funções que são contadas. Se o número de chamadas Detect ou BreakSentence feitas exceder 100 vezes o número de outros métodos contabilizados, a Microsoft se reservará o direito de restringir o uso dos métodos Detect e BreakSentence.


Veja mais informações sobre contagens de caracteres em [Perguntas frequentes sobre o Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
