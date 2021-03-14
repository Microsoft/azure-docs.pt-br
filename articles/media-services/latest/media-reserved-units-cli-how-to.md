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
ms.openlocfilehash: a07c4a20b854e09daf3b320b8c99757ca99b2578
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213803"
---
# <a name="how-to-scale-media-reserved-units"></a>Como dimensionar unidades reservadas de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como dimensionar MRSs (unidades reservadas de mídia) para uma codificação mais rápida.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta de Serviços de Mídia](./create-account-howto.md).

Entenda as [unidades reservadas de mídia](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Unidades com a CLI reservadas de mídia de escala

Execute o comando `mru`.

O comando [az ams account mru](/cli/azure/ams/account/mru) a seguir define as Unidades Reservadas de Mídia na conta "amsaccount" usando os parâmetros **count** e **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Cobrança

Você é cobrado com base em quantos minutos as unidades reservadas de mídia são provisionadas em sua conta. Isso ocorre independentemente de qualquer trabalho em execução em sua conta. Para obter uma explicação detalhada, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Próxima etapa

[Analisar vídeos](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Confira também

* [Cotas e limites](limits-quotas-constraints.md)
