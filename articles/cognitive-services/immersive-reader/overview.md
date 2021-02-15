---
title: O que é a Leitura Avançada?
titleSuffix: Azure Cognitive Services
description: A Leitura Avançada é uma ferramenta projetada para ajudar pessoas com diferenças de aprendizado ou ajudar novos leitores e aprendizes do idioma com a compreensão da leitura.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: leitores, aprendizes de idioma, exibir imagens, aprimorar leitura, ler conteúdo, traduzir
ms.openlocfilehash: 0661bd5f917a2bf071f11393ad8ac9af53ddfe97
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631229"
---
# <a name="what-is-immersive-reader"></a>O que é a Leitura Avançada?

A [Leitura Avançada](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente desenvolvida que implementa técnicas comprovadas para melhorar a compreensão de leitura para novos leitores, aprendizes da idioma e pessoas com diferenças de aprendizado, como dislexia. Com a biblioteca de clientes da Leitura Avançada, você pode aproveitar a mesma tecnologia usada no Microsoft Word e no Microsoft One para aprimorar os seus aplicativos Web. 

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>Usar a Leitura Avançada para aprimorar a acessibilidade da leitura 

A Leitura Avançada foi desenvolvida para tornar a leitura mais fácil e mais acessível a todos. Vamos dar uma olhada em alguns dos principais recursos da Leitura Avançada.

### <a name="isolate-content-for-improved-readability"></a>Isolar o conteúdo para legibilidade aprimorada

A Leitura Avançada isola o conteúdo para aprimorar a legibilidade. 

  ![Isolar o conteúdo para aprimorar a legibilidade com a Leitura Avançada](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Exibir imagens de palavras comuns

Para termos comumente usados, a Leitura Avançada exibirá uma imagem.

  ![Dicionário de Imagens com a Leitura Avançada](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>Realçar partes da fala

A Leitura Avançada pode ser usada para ajudar os aprendizes a entenderem partes da fala e da gramática realçando verbos, substantivos, pronomes e muito mais.

  ![Mostrar partes da fala com a Leitura Avançada](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Ler conteúdo em voz alta

A síntese de fala (ou conversão de texto em fala) está inclusa no serviço de Leitura Avançada, que permite que os leitores selecionem o texto a ser lido em voz alta. 

  ![Ler textos em voz alta com a Leitura Avançada](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Traduzir conteúdo em tempo real

A Leitura Avançada pode traduzir o texto em vários idiomas em tempo real. Isso é útil para aprimorar a compreensão dos leitores que estão aprendendo um novo idioma.

  ![Traduzir textos com a Leitura Avançada](./media/translation.png)

### <a name="split-words-into-syllables"></a>Dividir palavras em silabas

Com a Leitura Avançada, você pode dividir palavras em sílabas para aprimorar a legibilidade ou para ouvir a pronúncia de novas palavras.

  ![Dividir palavras em sílabas com a Leitura Avançada](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Como a Leitura Avançada funciona?

A Leitura Avançada é um aplicativo Web autônomo. Quando invocado usando a biblioteca de clientes da Leitura Avançada, ele é exibido na parte superior do aplicativo Web existente em um `iframe`. Quando o aplicativo Web chama o serviço de Leitura Avançada, você especifica o conteúdo para mostrar o leitor. A biblioteca de clientes da Leitura Avançada administra a criação e o estilo do `iframe`, bem como a comunicação com o serviço de back-end da Leitura Avançada. O serviço de Leitura Avançada processa o conteúdo de partes da fala, conversão de texto em fala, tradução e muito mais.

## <a name="get-started-with-immersive-reader"></a>Introdução à Leitura Avançada

A biblioteca de clientes da Leitura Avançada está disponível em C#, JavaScript, Java (Android), Kotlin (Android) e Swift (iOS). Comece com:

* [Início Rápido: Usar a biblioteca de clientes de Leitura Avançada](quickstarts/client-libraries.md)

## <a name="next-steps"></a>Próximas etapas

Introdução à Leitura Avançada:

* Leia a [Referência da biblioteca de clientes de Leitura Avançada](./reference.md)
* Explore a [Biblioteca de clientes de Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
