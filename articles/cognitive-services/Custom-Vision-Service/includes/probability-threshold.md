---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423598"
---
Observe o controle deslizante **Limite de Probabilidade** no painel à esquerda da guia **Desempenho**. Esse é o nível de confiança que uma previsão precisa ter para ser considerada correto (para fins de cálculo de precisão e recall). 

Quando você interpreta chamadas de previsão com um limite de probabilidade alto, elas tendem a retornar resultados com alta precisão às custas do recall: as classificações detectadas são corretas, mas muitas permanecem não detectadas. Um limite de probabilidade baixo tem o resultado oposto: a maior parte das classificações reais é detectada, mas há mais falsos positivos dentro desse conjunto. Com isso em mente, você deve definir o limite de probabilidade de acordo com as necessidades específicas do seu projeto. Posteriormente, ao receber os resultados de previsão no lado do cliente, você deve usar o mesmo valor de limite de probabilidade usado aqui.