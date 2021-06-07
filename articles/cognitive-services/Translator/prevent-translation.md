---
title: Impedir tradução de conteúdo-Tradutor
titleSuffix: Azure Cognitive Services
description: Impedir tradução de conteúdo com o tradutor. O tradutor permite que você marque o conteúdo para que ele não seja traduzido.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897469"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Como impedir a tradução de conteúdo com o tradutor

O tradutor permite que você marque o conteúdo para que ele não seja traduzido. Por exemplo, você pode querer marcar o código, um nome de marca ou uma palavra/frase que não faça sentido quando localizada.

## <a name="methods-for-preventing-translation"></a>Métodos para evitar a tradução

1. Marcar seu conteúdo com `notranslate`. É por design que isso funciona somente quando o TextType de entrada é definido como HTML

   Exemplo:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Marcar seu conteúdo com `translate="no"`. Isso só funciona quando o texto de entrada é definido como HTML

   Exemplo:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. use o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

4. Não transmita a cadeia de caracteres para o tradutor para tradução.

5. Tradutor personalizado: Use um [dicionário no Tradutor personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com uma probabilidade de 100%.


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Usar a operação de conversão para traduzir o texto](reference/v3-0-translate.md)
