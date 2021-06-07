---
title: incluir arquivo
description: arquivo de inclusão
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80272675"
---
Você pode criar vários serviços em uma assinatura. Cada um pode ser provisionado em uma camada específica. Você é limitado apenas pelo número de serviços permitidos em cada camada. Por exemplo, você pode criar até 12 serviços na camada Básico e outros 12 serviços na camada S1 dentro da mesma assinatura. Para obter mais informações sobre as camadas, confira [Escolher uma camada ou um SKU para o Azure Cognitive Search](../articles/search/search-sku-tier.md).

Os limites de serviço máximos podem ser aumentados mediante solicitação. Se você precisar de mais serviços na mesma assinatura, entre em contato com o Suporte do Azure.

| Recurso            | Gratuito<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Quantidade máxima de serviços    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Escala máxima em (SU) unidades de pesquisa<sup>2</sup> |N/D |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Gratuito é baseado em recursos compartilhados, não dedicados. Não há suporte para escalar verticalmente nos recursos compartilhados.

<sup>2</sup> As unidades de pesquisa são unidades de cobrança, alocadas como uma *réplica* ou como uma *partição*. Você precisa de ambos de recursos para as operações de armazenamento, indexação e consulta. Para saber mais sobre computação de SU, consulte [Escalar os níveis de recursos para cargas de trabalho de índice e consulta](../articles/search/search-capacity-planning.md). 