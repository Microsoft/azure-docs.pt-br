---
title: Solucionar problemas de ConstrainedAllocationFailed ao implantar um serviço de nuvem no Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção ConstrainedAllocationFailed ao implantar um serviço de nuvem no Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520872"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Solucionar problemas de ConstrainedAllocationFailed ao implantar um serviço de nuvem no Azure

Neste artigo, você solucionará problemas de falhas de alocação em que os serviços de nuvem do Azure não podem ser implantados devido a restrições.

Microsoft Azure aloca quando você está:

- Atualizando instâncias de serviços de nuvem

- Adicionando novas instâncias de função Web ou de trabalho

- Implantando instâncias em um serviço de nuvem

Ocasionalmente, você pode receber erros durante essas operações mesmo antes de alcançar o limite de assinatura do Azure.

> [!TIP]
> As informações também poderão ser úteis caso você pretenda implantar serviços.

## <a name="symptom"></a>Sintoma

Em portal do Azure, navegue até o serviço de nuvem e, na barra lateral, selecione *logs de operação (clássico)* para exibir os logs.

Quando você estiver inspecionando os logs do serviço de nuvem, verá a seguinte exceção:

|Tipo de exceção  |Mensagem de erro  |
|---------|---------|
|ConstrainedAllocationFailed     |A operação do Azure ' `{Operation ID}` ' falhou com o código COMPUTE. ConstrainedAllocationFailed. Detalhes: falha na alocação; Não é possível satisfazer as restrições na solicitação. A implantação solicitada do novo serviço está associada a um Grupo de Afinidades ou é destinada a uma Rede Virtual, ou há uma implantação existente para esse serviço hospedado. Essas condições restringem a nova implantação para os recursos específicos do Microsoft Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. De forma alternativa, remova as restrições mencionadas acima ou tente implantar em uma região diferente, se possível.|

## <a name="cause"></a>Causa

Há um problema de capacidade com a região ou o cluster no qual você está implantando. Ele ocorre quando o SKU de recurso que você selecionou não está disponível para o local especificado.

> [!NOTE]
> Quando o primeiro nó de um serviço de nuvem é implantado, ele é *fixado* em um pool de recursos. Um pool de recursos pode ser um único cluster ou um grupo de clusters.
>
> Ao longo do tempo, os recursos nesse pool de recursos podem se tornar totalmente utilizados. Se um serviço de nuvem fizer uma solicitação de alocação para recursos adicionais quando recursos insuficientes estiverem disponíveis no pool de recursos fixado, a solicitação resultará em uma [falha de alocação](cloud-services-allocation-failures.md).

## <a name="solution"></a>Solução

Nesse cenário, você deve selecionar uma região ou SKU diferente para implantar o serviço de nuvem. Antes de implantar ou atualizar seu serviço de nuvem, você pode determinar quais SKUs estão disponíveis em uma região ou zona de disponibilidade. Siga os processos do [CLI do Azure](#list-skus-in-region-using-azure-cli), do [PowerShell](#list-skus-in-region-using-powershell)ou da [API REST](#list-skus-in-region-using-rest-api) abaixo.

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais soluções de falha de alocação e entender melhor como elas são geradas:

> [!div class="nextstepaction"]
> [Falhas de alocação (serviços de nuvem)](cloud-services-allocation-failures.md)

Se o problema do Azure não for abordado neste artigo, visite os fóruns do Azure no [msdn e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou postar para [@AzureSupport no Twitter](https://twitter.com/AzureSupport). Você também pode enviar uma solicitação de suporte do Azure. Para enviar uma solicitação de suporte na página [Suporte do Azure](https://azure.microsoft.com/support/options/), selecione *Obter suporte*.
