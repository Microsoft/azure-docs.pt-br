---
title: Nuvens e regiões para os serviços de mídia do Azure v3
description: Este artigo fala sobre as URLs usadas para pontos de extremidade e código para regiões.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954320"
---
# <a name="regional-code-names-and-endpoints"></a>Nomes de código regionais e pontos de extremidade

### <a name="region-code-name"></a>Nome do código da região

Quando o parâmetro **Location** é usado em um comando ou solicitação, você precisa fornecer o nome do código de região como o valor de **local** . Para obter o nome do código da região em que sua conta está e para a qual sua chamada deve ser roteada, você pode executar a seguinte linha em CLI do Azure.

```azurecli-interactive
az account list-locations
```

Depois de executar a linha mostrada acima, você obterá uma lista de todas as regiões do Azure. Navegue para a região do Azure que tenha o *displayName* que você está procurando e use seu valor *name* para o parâmetro **location**.

Por exemplo, para a região Oeste dos EUA 2 (exibida abaixo) do Azure, você usará "westus2" para o parâmetro **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Pontos de extremidade  

É importante conhecer os pontos de extremidade a seguir ao conectar as contas de Serviços de Mídia em diferentes nuvens nacionais do Azure.

### <a name="global-azure"></a>Azure Global

| Serviço | Ponto de extremidade |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Autenticação | `https://login.microsoftonline.com/` |
| Público do token | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Serviço | Ponto de extremidade |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Autenticação | `https://login.microsoftonline.us/` |
| Público do token | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Alemanha

> [!NOTE]
> Os pontos de extremidade do Azure Alemanha só se aplicam às nuvens soberanas na Alemanha.

| Serviço | Ponto de extremidade |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Autenticação | `https://login.microsoftonline.de/` |
| Público do token | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Serviço | Ponto de extremidade |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Autenticação | `https://login.chinacloudapi.cn/` |
| Público do token |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Veja também

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Nomes de código regionais e pontos de extremidade](azure-regions-code-names.md)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia v3](media-services-overview.md)
