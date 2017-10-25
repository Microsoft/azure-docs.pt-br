---
title: "Erros de SKU do Azure não disponível | Microsoft Docs"
description: "Descreve como solucionar o erro \"SKU não disponível\" durante a implantação."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 25cea4ae23471d182105ca3f720aaf74f81bf8c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolva erros de SKU não disponível

Este artigo descreve como resolver o erro **SkuNotAvailable**.

## <a name="symptom"></a>Sintoma

Ao implantar um recurso (normalmente, uma máquina virtual), você recebe o seguinte código de erro e a mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Você recebe esse erro quando o recurso SKU selecionado (como o tamanho da VM) não está disponível para o local escolhido.

## <a name="solution"></a>Solução

Para resolver esse problema, você precisa determinar quais SKUs estão disponíveis em uma região. Você pode usar o PowerShell, o portal ou uma operação REST para localizar SKUs disponíveis.

### <a name="solution-1"></a>Solução 1

Use o comando [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) no PowerShell. Filtre os resultados por local. Você deve ter a versão mais recente do PowerShell para esse comando.

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
```

Os resultados incluem uma lista de SKUs para o local e as restrições desse SKU.

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

### <a name="solution-2"></a>Solução 2

Na CLI do Azure, use o comando `az vm list-skus`. Você pode usar o `grep` ou um utilitário semelhante para filtrar a saída.

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

### <a name="solution-3"></a>Solução 3

Use o [portal](https://portal.azure.com). Faça logon no portal e adicione um recurso por meio da interface. Ao definir os valores, verá os SKUs disponíveis para esse recurso. Não é necessário concluir a implantação.

![SKUs disponíveis](./media/resource-manager-sku-not-available-errors/view-sku.png)

### <a name="solution-4"></a>Solução 4

Use a API REST para máquinas virtuais. Envie a seguinte solicitação:

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

Ele retorna SKUs e regiões disponíveis no seguinte formato:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

Caso você não consiga encontrar um SKU adequado na região ou em uma região alternativa que atende às suas necessidades de negócios, envie uma [solicitação de SKU](https://aka.ms/skurestriction) para o Suporte do Azure.