---
title: Como preparar o conteúdo HTML para o leitor de imersão
titleSuffix: Azure Cognitive Services
description: Saiba como iniciar o leitor de imersão usando HTML, JavaScript, Python, Android ou iOS. O leitor de imersão usa técnicas comprovadas para melhorar a compreensão da leitura para aprendizes de idioma, leitores emergentes e alunos com diferenças de aprendizado.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619982"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Como preparar o conteúdo HTML para o leitor de imersão

Este artigo mostra como estruturar o HTML e recuperar o conteúdo, para que ele possa ser usado pelo leitor de imersão.

## <a name="prepare-the-html-content"></a>Preparar o conteúdo HTML

Coloque o conteúdo que você deseja renderizar no leitor de imersão dentro de um elemento de contêiner. Certifique-se de que o elemento contêiner tenha um exclusivo `id` . O leitor de imersão fornece suporte para elementos HTML básicos, consulte a [referência](reference.md#html-support) para obter mais informações.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Obter o conteúdo HTML em JavaScript

Use o `id` do elemento contêiner para obter o conteúdo HTML em seu código JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Inicie o leitor de imersão com seu conteúdo HTML

Ao chamar `ImmersiveReader.launchAsync` , defina a propriedade da parte `mimeType` como `text/html` para habilitar o processamento de HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](reference.md)