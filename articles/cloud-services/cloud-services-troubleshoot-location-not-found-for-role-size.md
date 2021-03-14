---
title: Solucionar problemas de LocationNotFoundForRoleSize ao implantar um serviço de nuvem (clássico) no Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção LocationNotFoundForRoleSize ao implantar um serviço de nuvem (clássico) no Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: b11aedb52be3c263c781c2ac68d1d5197ba4def2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744222"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Solucionar problemas de LocationNotFoundForRoleSize ao implantar um serviço de nuvem (clássico) no Azure

Neste artigo, você solucionará problemas de falhas de alocação em que um tamanho de VM (máquina virtual) não está disponível quando você implanta um serviço de nuvem do Azure (clássico).

Quando você implanta instâncias em um serviço de nuvem (clássico) ou adiciona novas instâncias de função Web ou de trabalho, Microsoft Azure aloca recursos de computação.

Ocasionalmente, você pode receber erros durante essas operações mesmo antes de alcançar o limite de assinatura do Azure.

> [!TIP]
> As informações também poderão ser úteis caso você pretenda implantar serviços.

## <a name="symptom"></a>Sintoma

Em portal do Azure, navegue até o serviço de nuvem (clássico) e, na barra lateral, selecione *log de operação (clássico)* para exibir os logs.

![Imagem mostra a folha log de operação (clássico).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Quando você estiver inspecionando os logs do serviço de nuvem (clássico), verá a seguinte exceção:

|Tipo de exceção  |Mensagem de erro  |
|---------|---------|
|LocationNotFoundForRoleSize     |A operação ' `{Operation ID}` ' falhou: ' a camada de VM solicitada não está disponível atualmente na região ( `{Region ID}` ) para esta assinatura. Tente outra camada ou implante-a em um local diferente. '.|

## <a name="cause"></a>Causa

Há um problema de capacidade com a região ou o cluster no qual você está implantando. A exceção *LocationNotFoundForRoleSize* ocorre quando o SKU de recurso que você selecionou (tamanho da VM) não está disponível para a região especificada.

## <a name="solution"></a>Solução

Nesse cenário, você deve selecionar uma região ou SKU diferente para implantar seu serviço de nuvem (clássico) no. Antes de implantar ou atualizar seu serviço de nuvem (clássico), você pode determinar quais SKUs estão disponíveis em uma região ou zona de disponibilidade. Siga os processos do [CLI do Azure](#list-skus-in-region-using-azure-cli), do [PowerShell](#list-skus-in-region-using-powershell)ou da [API REST](#list-skus-in-region-using-rest-api) abaixo.

### <a name="list-skus-in-region-using-azure-cli"></a>Listar SKUs na região usando CLI do Azure

Você pode usar o comando [AZ VM List-SKUs](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) .

- Use o `--location` parâmetro para filtrar a saída para o local que você está usando.
- Use o parâmetro `--size` para procurar por um nome de tamanho parcial.
- Para obter mais informações, consulte o guia [resolver erros do SKU não disponível](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Por exemplo:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Resultados de exemplo:** ![ CLI do Azure saída da execução do comando ' AZ VM List-SKUs--Location southcentralus--size Standard_F--output Table ', que mostra as SKUs disponíveis.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Listar SKUs na região usando o PowerShell

Você pode usar o comando [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) .

- Filtre os resultados por local.
- Você deve ter a versão mais recente do PowerShell para esse comando.
- Para obter mais informações, consulte o guia [resolver erros do SKU não disponível](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Por exemplo:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Alguns outros comandos úteis:**

Filtrar os locais que contêm o tamanho (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrar todos os locais que contêm o tamanho (v3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Listar SKUs na região usando a API REST

Você pode usar a operação [SKUs de recurso-lista](https://docs.microsoft.com/rest/api/compute/resourceskus/list) . Ele retorna SKUs e regiões disponíveis no seguinte formato:

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais soluções de falha de alocação e entender melhor como elas são geradas:

> [!div class="nextstepaction"]
> [Falhas de alocação-serviço de nuvem (clássico)](cloud-services-allocation-failures.md)

Se o problema do Azure não for abordado neste artigo, visite os fóruns do Azure no [msdn e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou postar para [@AzureSupport no Twitter](https://twitter.com/AzureSupport). Você também pode enviar uma solicitação de suporte do Azure. Para enviar uma solicitação de suporte na página [Suporte do Azure](https://azure.microsoft.com/support/options/), selecione *Obter suporte*.
