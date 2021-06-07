---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467183"
---
Examine os valores de `"confidence"` para cada resultado de chave-valor no nó `"pageResults"`. Você também deve examinar as pontuações de confiança no nó `"readResults"`, que correspondem à operação de leitura de texto. A confiança dos resultados de leitura não afeta a confiança dos resultados de extração de chave-valor, portanto, você deve verificar ambos.
* Se as pontuações de confiança da operação de leitura estiverem baixas, tente aprimorar a qualidade de seus documentos de entrada (consulte [Requisitos de entrada](../overview.md#input-requirements)).
* Se as pontuações de confiança para a operação de extração de chave-valor estiverem baixas, verifique se os documentos sendo analisados são do mesmo tipo que os documentos usados no conjunto de treinamento. Se os documentos no conjunto de treinamento tiverem variações de aparência, considere dividi-los em pastas diferentes e treinar um modelo para cada variação.

As pontuações de confiança que você visa dependerão do seu caso de uso, mas geralmente é uma boa prática visar uma pontuação de 80% ou acima. Para casos mais confidenciais, como ler registros médicos ou demonstrativos, é recomendável uma pontuação de 100%.