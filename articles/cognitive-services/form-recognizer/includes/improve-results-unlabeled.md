---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "75379277"
---
Examine os valores de `"confidence"` para cada resultado de chave-valor no nó `"pageResults"`. Você também deve examinar as pontuações de confiança no nó `"readResults"`, que correspondem à operação de leitura de texto. A confiança dos resultados de leitura não afeta a confiança dos resultados de extração de chave-valor, portanto, você deve verificar ambos.
* Se as pontuações de confiança da operação de leitura estiverem baixas, tente aprimorar a qualidade de seus documentos de entrada (consulte [Requisitos de entrada](../overview.md#input-requirements)).
* Se as pontuações de confiança para a operação de extração de chave-valor estiverem baixas, verifique se os documentos sendo analisados são do mesmo tipo que os documentos usados no conjunto de treinamento. Se os documentos no conjunto de treinamento tiverem variações de aparência, considere dividi-los em pastas diferentes e treinar um modelo para cada variação.

As pontuações de confiança que você visa dependerão do seu caso de uso, mas geralmente é uma boa prática visar uma pontuação de 80% ou acima. Para casos mais confidenciais, como ler registros médicos ou demonstrativos, é recomendável uma pontuação de 100%.