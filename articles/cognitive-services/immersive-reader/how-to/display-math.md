---
title: Exibir matemática no leitor de imersão
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como exibir cálculos no leitor de imersão.
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 75b748604e8faa502970ac71e3fec4fae2774c19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334507"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Como exibir cálculos no leitor de imersão

O leitor de imersão pode exibir matemática quando fornecido na forma de[MathML](https://developer.mozilla.org/docs/Web/MathML)(matemático Markup Language).
O tipo MIME pode ser definido por meio da [parte](../reference.md#chunk)do leitor de imersão. Consulte [tipos de MIME com suporte](../reference.md#supported-mime-types) para obter mais informações.

## <a name="send-math-to-the-immersive-reader"></a>Enviar matemática para o leitor de imersão
Para enviar cálculos para o leitor de imersão, forneça uma parte contendo MathML e defina o tipo MIME como ```application/mathml+xml``` ;

Por exemplo, se o conteúdo for o seguinte:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Em seguida, você pode exibir seu conteúdo usando o JavaScript a seguir.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Ao iniciar o leitor de imersão, você verá:

![Matemática no leitor de imersão](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](../reference.md)