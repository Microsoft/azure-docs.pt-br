---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308992"
---
## <a name="access-the-media-services-api"></a>Instalar a API de Serviços de Mídia

Use a autenticação de entidade de serviço do Azure AD para se conectar às APIs dos Serviços de Mídia do Azure. O comando a seguir cria um aplicativo do Azure AD e anexa uma entidade de serviço à conta. Você deve usar os valores retornados para configurar seu aplicativo.

Antes de executar o script, você pode substituir `amsaccount` e `amsResourceGroup` pelos nomes escolhidos na hora de criar esses recursos. `amsaccount` é o nome da conta dos Serviços de Mídia do Azure em que a entidade de serviço será anexada.

O comando a seguir retorna uma saída `json`:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Esse comando produz uma resposta semelhante a esta:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Se você gostaria de obter `xml` na resposta, use o seguinte comando:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
