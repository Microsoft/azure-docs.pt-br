---
title: Evitar a tradução de conteúdo – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Evitar a tradução de conteúdo com a API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f9b48b094713f6ee141c5c15d5636ca965ad61b9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595233"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como evitar a tradução de conteúdo com a API de Tradução de Texto

A API de Tradução de Texto permite que marcar conteúdo para que não seja traduzido. Por exemplo, você talvez queira marcar o código, um nome de marca ou uma palavra/frase que não precisa ser traduzida.

## <a name="methods-for-preventing-translation"></a>Métodos para evitar a tradução
1. Usar escape para uma tag do Twitter @somethingtopassthrough ou #somethingtopassthrough. Não usar escape após a tradução.

2. Marcar seu conteúdo com `notranslate`.

   Exemplo:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. use o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

4. Não passe a cadeia de caracteres para a API de Tradução de Texto para tradução.

5. Tradutor Personalizado: use um [dicionário do Tradutor Personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com 100% de probabilidade.


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Evitar a tradução em sua chamada à API do Tradutor](reference/v3-0-translate.md)
