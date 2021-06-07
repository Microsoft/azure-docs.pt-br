---
title: Regiões nas quais o Video Indexer está disponível – Azure
titleSuffix: Azure Media Services
description: Este artigo fala sobre regiões do Azure nas quais os serviços de mídia do Azure Video Indexer estão disponíveis.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530923"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiões do Azure nas quais existe um Video Indexer

As APIs do Video Indexer contêm um parâmetro **location** que deve ser definido com a região do Azure para a qual a chamada deve ser encaminhada. Ela precisa ser uma [região do Azure na qual o Video Indexer está disponível](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Locais

O `location` parâmetro deve receber o nome do código de região do Azure como seu valor. Se você estiver usando Video Indexer no modo de visualização, deverá colocar `"trial"` como o valor. `trial` é o valor padrão para o `location` parâmetro. Caso contrário, para obter o nome do código da região do Azure em que sua conta está e para a qual sua chamada deve ser roteada, você pode usar o portal do Azure ou executar um comando [CLI do Azure](/cli/azure) .

### <a name="azure-portal"></a>Portal do Azure

1. Conecte-se ao site do [Video Indexer](https://www.videoindexer.ai/).
1. Selecione **contas de usuário** no canto superior direito da página.
1. Localize o local da sua conta no canto superior direito.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Localidade":::
    
###  <a name="cli-command"></a>Comando da CLI

```azurecli-interactive
az account list-locations
```

Depois de executar a linha mostrada acima, você obterá uma lista de todas as regiões do Azure. Navegue para a região do Azure que tenha o *displayName* que você está procurando e use seu valor *name* para o parâmetro **location**.

Por exemplo, para a região Oeste dos EUA 2 (exibida abaixo) do Azure, você usará "westus2" para o parâmetro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Próximas etapas

- [Personalizar o modelo de Linguagem usando APIs](customize-language-model-with-api.md)
- [Personalizar o modelo de Marcas usando APIs](customize-brands-model-with-api.md)
- [Personalizar o modelo de Pessoa usando APIs](customize-person-model-with-api.md)
