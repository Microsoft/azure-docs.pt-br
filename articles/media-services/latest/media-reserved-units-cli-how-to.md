---
title: CLI de dimensionar unidades reservadas de mídia (MRUs)
description: Este tópico mostra como usar a CLI para dimensionar processamento de mídia com os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: b1c98bfa6b2cf45a59b70126001442ed80659668
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955878"
---
# <a name="how-to-scale-media-reserved-units"></a>Como dimensionar unidades reservadas de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como dimensionar MRSs (unidades reservadas de mídia) para uma codificação mais rápida.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta de Serviços de Mídia](./create-account-howto.md).

Entenda as [unidades reservadas de mídia](concept-media-reserved-units.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Unidades com a CLI reservadas de mídia de escala

Execute o comando `mru`.

O comando [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest) a seguir define as Unidades Reservadas de Mídia na conta "amsaccount" usando os parâmetros **count** e **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Cobrança

Você é cobrado com base em quantos minutos as unidades reservadas de mídia são provisionadas em sua conta. Isso ocorre independentemente de qualquer trabalho em execução em sua conta. Para obter uma explicação detalhada, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Próxima etapa

[Analisar vídeos](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Veja também

* [Cotas e limites](limits-quotas-constraints.md)
