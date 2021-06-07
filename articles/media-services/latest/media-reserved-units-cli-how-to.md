---
title: CLI de dimensionar unidades reservadas de mídia (MRUs)
description: Este tópico mostra como usar a CLI para dimensionar processamento de mídia com os Serviços de Mídia do Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870153"
---
# <a name="how-to-scale-media-reserved-units"></a>Como dimensionar unidades reservadas de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como dimensionar MRSs (unidades reservadas de mídia) para uma codificação mais rápida.

> [!WARNING]
> Esse comando não funcionará mais para contas de serviços de mídia criadas com a versão 2020-05-01 da API ou posterior. Para essas contas, as unidades reservadas de mídia não são mais necessárias, pois o sistema será dimensionado e reduzido automaticamente com base na carga. Se você não vir a opção de gerenciar MRUs no portal do Azure, você está usando uma conta que foi criada com a API 2020-05-01 ou posterior.

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
