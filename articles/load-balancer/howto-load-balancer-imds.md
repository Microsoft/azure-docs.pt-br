---
title: Recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Comece a aprender como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 95d0e1ceb9e05ce58f388c3f88dc98b2cf6a0cc5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559580"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)

## <a name="prerequisites"></a>Pré-requisitos

* Use a [versão mais recente da API](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) para sua solicitação.

## <a name="sample-request-and-response"></a>Exemplo de solicitação e resposta
> [!IMPORTANT]
> Este exemplo ignora proxies. Você **deve** ignorar proxies ao consultar IMDS. Para obter mais informações, consulte [proxies](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> O parâmetro-NoProxy foi introduzido no PowerShell 6,0. Se você estiver usando uma versão mais antiga do PowerShell, remove-NoProxy no corpo da solicitação e certifique-se de que você não está usando um proxy ao recuperar informações de IMDS. Saiba mais [aqui](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Resposta de exemplo

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Próximas etapas
[Códigos de erro comuns e etapas de solução de problemas](troubleshoot-load-balancer-imds.md)

Saiba mais sobre o [serviço de metadados de instância do Azure](../virtual-machines/windows/instance-metadata-service.md)

[Recuperar todos os metadados de uma instância](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Implantar um balanceador de carga padrão](quickstart-load-balancer-standard-public-portal.md)