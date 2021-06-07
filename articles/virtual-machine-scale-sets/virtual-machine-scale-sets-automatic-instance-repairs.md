---
title: Reparos automáticos de instância com conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como configurar a política de reparos automáticos para instâncias de VM em um conjunto de dimensionamento
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff67ac4be32142848a12185199d63db5a14e6c34
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501848"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Reparos automáticos de instância para conjuntos de dimensionamento de máquinas virtuais do Azure

A habilitação de reparos de instância automática para conjuntos de dimensionamento de máquinas virtuais do Azure ajuda a obter alta disponibilidade para aplicativos mantendo um conjunto de instâncias íntegras. Se uma instância no conjunto de dimensionamento for considerada não íntegra conforme relatado pela extensão de [integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou pelas [investigações de integridade do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md), esse recurso executará automaticamente o reparo da instância excluindo a instância não íntegra e criando uma nova para substituí-la.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisitos para usar os reparos automáticos da instância

**Habilitar o monitoramento de integridade do aplicativo para o conjunto de dimensionamento**

O conjunto de dimensionamento deve ter o monitoramento de integridade do aplicativo para as instâncias habilitadas. Isso pode ser feito usando a [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as [investigações de integridade do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Somente um deles pode ser habilitado por vez. A extensão de integridade do aplicativo ou as investigações do balanceador de carga executam ping no ponto de extremidade do aplicativo configurado em instâncias de máquina virtual para determinar o status de integridade do aplicativo. Esse status de integridade é usado pelo orquestrador do conjunto de dimensionamento para monitorar a integridade da instância e executar reparos quando necessário.

**Configurar o ponto de extremidade para fornecer o status de integridade**

Antes de habilitar a política de reparos de instância automática, verifique se as instâncias do conjunto de dimensionamento têm o ponto de extremidade do aplicativo configurado para emitir o status de integridade do aplicativo Quando uma instância retorna o status 200 (OK) nesse ponto de extremidade do aplicativo, a instância é marcada como "íntegra". Em todos os outros casos, a instância é marcada como "não íntegra", incluindo os seguintes cenários:

- Quando não há nenhum ponto de extremidade de aplicativo configurado dentro das instâncias de máquina virtual para fornecer o status de integridade do aplicativo
- Quando o ponto de extremidade do aplicativo está configurado incorretamente
- Quando o ponto de extremidade do aplicativo não está acessível

Para instâncias marcadas como "não íntegras", os reparos automáticos são disparados pelo conjunto de dimensionamento. Verifique se o ponto de extremidade do aplicativo está configurado corretamente antes de habilitar a política de reparos automáticos para evitar reparos de instância não intencional, enquanto o ponto de extremidade está sendo configurado.

**Número máximo de instâncias no conjunto de dimensionamento**

Esse recurso está disponível no momento apenas para conjuntos de dimensionamento que têm um máximo de 200 instâncias. O conjunto de dimensionamento pode ser implantado como um único grupo de posicionamento ou um grupo de vários locais. no entanto, a contagem de instâncias não poderá ser superior a 200 se o reparo automático de instância estiver habilitado para o conjunto de dimensionamento.

**Versão da API**

A política de reparos automáticos tem suporte para a API de computação versão 2018-10-01 ou superior.

**As restrições no recurso ou na assinatura se movem**

As movimentações de recursos ou assinaturas não têm suporte no momento para conjuntos de dimensionamento quando o recurso de reparos automáticos está habilitado.

**Restrição para conjuntos de dimensionamento do Service Fabric**

Atualmente, não há suporte para este recurso em conjuntos de dimensionamento do Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Como funcionam os reparos automáticos da instância?

O recurso de reparo automático de instância depende do monitoramento de integridade de instâncias individuais em um conjunto de dimensionamento. As instâncias de VM em um conjunto de dimensionamento podem ser configuradas para emitir o status de integridade do aplicativo usando a [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as investigações de integridade do [balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Se uma instância for considerada não íntegra, o conjunto de dimensionamento executará a ação de reparo excluindo a instância não íntegra e criando uma nova para substituí-la. O modelo de conjunto de dimensionamento de máquinas virtuais mais recente é usado para criar a nova instância. Esse recurso pode ser habilitado no modelo do conjunto de dimensionamento de máquinas virtuais usando o objeto *automaticRepairsPolicy* .

### <a name="batching"></a>Separação em lotes

As operações automáticas de reparo de instância são executadas em lotes. Em um determinado momento, não mais do que 5% das instâncias no conjunto de dimensionamento são reparadas por meio da política de reparos automáticas. Isso ajuda a evitar a exclusão simultânea e a recriação de um grande número de instâncias, se elas não estiverem íntegras ao mesmo tempo.

### <a name="grace-period"></a>Período de carência

Quando uma instância passa por uma operação de alteração de estado devido a uma ação PUT, PATCH ou POST executada no conjunto de dimensionamento (por exemplo, refazer a imagem, reimplantar, atualizar, etc.), qualquer ação de reparo nessa instância é executada somente depois de aguardar o período de carência. O período de carência é a quantidade de tempo para permitir que a instância retorne ao estado íntegro. O período de carência começa após a conclusão da alteração de estado. Isso ajuda a evitar qualquer operação de reparo prematuro ou acidental. O período de carência é respeitado para qualquer instância recém-criada no conjunto de dimensionamento (incluindo aquele criado como resultado da operação de reparo). O período de carência é especificado em minutos no formato ISO 8601 e pode ser definido usando a propriedade *automaticRepairsPolicy. gracePeriod*. O período de carência pode variar entre 30 minutos e 90 minutos e tem um valor padrão de 30 minutos.

### <a name="suspension-of-repairs"></a>Suspensão de reparos 

Os conjuntos de dimensionamento de máquinas virtuais fornecem a capacidade de suspender temporariamente os reparos automáticos da instância, se necessário. O *ServiceState* para os reparos automáticos na propriedade *orchestrationServices* na exibição de instância do conjunto de dimensionamento de máquinas virtuais mostra o estado atual dos reparos automáticos. Quando um conjunto de dimensionamento é aceito em reparos automáticos, o valor do parâmetro *ServiceState* é definido como *running*. Quando os reparos automáticos são suspensos para um conjunto de dimensionamento, o parâmetro *ServiceState* é definido como *suspenso*. Se *automaticRepairsPolicy* for definido em um conjunto de dimensionamento, mas o recurso de reparos automáticos não estiver habilitado, o parâmetro *ServiceState* será definido como *não em execução*.

Se as instâncias recém-criadas para substituir as não íntegras em um conjunto de dimensionamento continuarem a permanecer não íntegras mesmo após a execução repetida das operações de reparo, como medida de segurança, a plataforma atualizará o *ServiceState* para que os reparos automáticos sejam *suspensos*. Você pode retomar os reparos automáticos novamente definindo o valor de *ServiceState* para que os reparos automáticos sejam *executados*. Instruções detalhadas são fornecidas na seção sobre como [Exibir e atualizar o estado do serviço da política de reparos automáticos](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) para seu conjunto de dimensionamento. 

O processo de reparos automáticos da instância funciona da seguinte maneira:

1. A [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as investigações de integridade do [balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md) executam ping no ponto de extremidade do aplicativo em cada máquina virtual no conjunto de dimensionamento para obter o status de integridade do aplicativo para cada instância.
2. Se o ponto de extremidade responder com um status 200 (OK), a instância será marcada como "íntegra". Em todos os outros casos (incluindo se o ponto de extremidade está inacessível), a instância é marcada como "não íntegro".
3. Quando uma instância é considerada não íntegra, o conjunto de dimensionamento dispara uma ação de reparo excluindo a instância não íntegra e criando uma nova para substituí-la.
4. Os reparos da instância são executados em lotes. Em um determinado momento, não mais do que 5% do total de instâncias no conjunto de dimensionamento são reparadas. Se um conjunto de dimensionamento tiver menos de 20 instâncias, os reparos serão feitos para uma instância não íntegra de cada vez.
5. O processo acima continua até que todas as instâncias não íntegras no conjunto de dimensionamento sejam reparadas.

## <a name="instance-protection-and-automatic-repairs"></a>Proteção de instância e reparos automáticos

Se uma instância em um conjunto de dimensionamento for protegida pela aplicação de uma das [políticas de proteção](./virtual-machine-scale-sets-instance-protection.md), os reparos automáticos não serão executados nessa instância. Isso se aplica às duas políticas de proteção: *proteger contra redução* e *proteção de ações de conjunto de dimensionamento* . 

## <a name="terminatenotificationandautomaticrepairs"></a>Encerrar notificação e reparos automáticos

Se o recurso de [notificação de encerramento](./virtual-machine-scale-sets-terminate-notification.md) estiver habilitado em um conjunto de dimensionamento, durante a operação de reparo automática, a exclusão de uma instância não íntegra seguirá a configuração de notificação de encerramento. Uma notificação de término é enviada por meio dos eventos agendados do serviço de metadados do Azure – e a exclusão da instância é atrasada durante o tempo limite de atraso configurado. No entanto, a criação de uma nova instância para substituir a não íntegra não aguarda a conclusão do tempo limite de atraso.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Habilitando a política de reparos automáticos ao criar um novo conjunto de dimensionamento

Para habilitar a política de reparos automáticos ao criar um novo conjunto de dimensionamento, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para a permissão desse recurso sejam atendidos. O ponto de extremidade do aplicativo deve ser configurado corretamente para instâncias do conjunto de dimensionamento para evitar o disparo de reparos indesejados enquanto o ponto de extremidade está sendo configurado. Para conjuntos de dimensionamento recém-criados, qualquer reparo de instância é executado somente após a espera pela duração do período de carência. Para habilitar o reparo automático de instância em um conjunto de dimensionamento, use o objeto *automaticRepairsPolicy* no modelo do conjunto de dimensionamento de máquinas virtuais.

Você também pode usar este [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) para implantar um conjunto de dimensionamento de máquinas virtuais com investigação de integridade do balanceador de carga e reparos de instância automática habilitados com um período de carência de 30 minutos.

### <a name="azure-portal"></a>Portal do Azure
 
As etapas a seguir habilitam a política de reparos automáticos ao criar um novo conjunto de dimensionamento.
 
1. Vá para **conjuntos de dimensionamento de máquinas virtuais**.
1. Selecione **+ Adicionar** para criar um novo conjunto de dimensionamento.
1. Vá para a guia **integridade** . 
1. Localize a seção de **integridade** .
1. Habilite a opção **monitorar integridade do aplicativo** .
1. Localize a seção **política de reparo automática** .
1. Ative **a opção de** **reparos automáticos** .
1. No **período de carência (min)**, especifique o período de carência em minutos; os valores permitidos estão entre 30 e 90 minutos. 
1. Quando terminar de criar o novo conjunto de dimensionamento, selecione o botão **revisar + criar** .

### <a name="rest-api"></a>API REST

O exemplo a seguir mostra como habilitar o reparo automático de instância em um modelo de conjunto de dimensionamento. Use a API versão 2018-10-01 ou superior.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

O recurso de reparo automático de instância pode ser habilitado ao criar um novo conjunto de dimensionamento usando o cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Este script de exemplo percorre a criação de um conjunto de dimensionamento e os recursos associados usando o arquivo de configuração: [criar um conjunto de dimensionamento de máquinas virtuais completo](./scripts/powershell-sample-create-complete-scale-set.md). Você pode configurar a política de reparos automáticos de instância adicionando os parâmetros *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* ao objeto de configuração para criar o conjunto de dimensionamento. O exemplo a seguir habilita o recurso com um período de carência de 30 minutos.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir habilita a política de reparos automáticos ao criar um novo conjunto de dimensionamento usando *[AZ vmss Create](/cli/azure/vmss#az-vmss-create)*. Primeiro, crie um grupo de recursos e, em seguida, crie um novo conjunto de dimensionamento com o período de carência da política de reparos automático definido como 30 minutos.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

O exemplo acima usa um balanceador de carga e uma investigação de integridade existentes para monitorar o status de integridade do aplicativo de instâncias. Se preferir usar uma extensão de integridade do aplicativo para monitoramento em vez disso, você pode criar um conjunto de dimensionamento, configurar a extensão de integridade do aplicativo e habilitar a política de reparos de instância automática usando a *atualização AZ vmss*, conforme explicado na próxima seção.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Habilitando a política de reparos automáticos ao atualizar um conjunto de dimensionamento existente

Antes de habilitar a política de reparos automáticos em um conjunto de dimensionamento existente, verifique se todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esse recurso foram atendidos. O ponto de extremidade do aplicativo deve ser configurado corretamente para instâncias do conjunto de dimensionamento para evitar o disparo de reparos indesejados enquanto o ponto de extremidade está sendo configurado. Para habilitar o reparo automático de instância em um conjunto de dimensionamento, use o objeto *automaticRepairsPolicy* no modelo do conjunto de dimensionamento de máquinas virtuais.

Depois de atualizar o modelo de um conjunto de dimensionamento existente, certifique-se de que o modelo mais recente seja aplicado a todas as instâncias da escala. Consulte a instrução sobre [como colocar as VMs atualizadas com o modelo de conjunto de dimensionamento mais recente](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Portal do Azure

Você pode modificar a política de reparos automáticos de um conjunto de dimensionamento existente por meio do portal do Azure. 
 
1. Acesse um conjunto de dimensionamento de máquinas virtuais existente.
1. Em **configurações** no menu à esquerda, selecione **integridade e reparo**.
1. Habilite a opção **monitorar integridade do aplicativo** .
1. Localize a seção **política de reparo automática** .
1. Ative **a opção de** **reparos automáticos** .
1. No **período de carência (min)**, especifique o período de carência em minutos; os valores permitidos estão entre 30 e 90 minutos. 
1. Quando terminar, selecione **Salvar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir habilita a política com o período de carência de 40 minutos. Use a API versão 2018-10-01 ou superior.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para modificar a configuração do recurso de reparo automático de instância em um conjunto de dimensionamento existente. O exemplo a seguir atualiza o período de carência para 40 minutos.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

Veja a seguir um exemplo de atualização da política de reparos automáticos de instância de um conjunto de dimensionamento existente, usando *[AZ vmss Update](/cli/azure/vmss#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Exibindo e atualizando o estado do serviço da política de reparos automáticos da instância

### <a name="rest-api"></a>API REST 

Use a [exibição obter instância](/rest/api/compute/virtualmachinescalesets/getinstanceview) com a versão de API 2019-12-01 ou superior para o conjunto de dimensionamento de máquinas virtuais para exibir o *ServiceState* para reparos automáticos na propriedade *orchestrationServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Use a API *setOrchestrationServiceState* com a versão de API 2019-12-01 ou superior em um conjunto de dimensionamento de máquinas virtuais para definir o estado de reparos automáticos. Depois que o conjunto de dimensionamento for aceito no recurso de reparos automáticos, você poderá usar essa API para suspender ou retomar os reparos automáticos para seu conjunto de dimensionamento. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>CLI do Azure 

Use o cmdlet [Get-Instance-View](/cli/azure/vmss#az-vmss-get-instance-view) para exibir o *ServiceState* para reparos automáticos de instância. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Use o cmdlet [set-Orchestration-Service-State](/cli/azure/vmss#az-vmss-set-orchestration-service-state) para atualizar o *ServiceState* para reparos automáticos de instância. Depois que o conjunto de dimensionamento for aceito no recurso de reparo automático, você poderá usar este cmdlet para suspender ou retomar os reparos automáticos para o conjunto de dimensionamento. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) com o parâmetro *InstanceView* para exibir o *ServiceState* para reparos automáticos de instância.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Use Set-AzVmssOrchestrationServiceState cmdlet para atualizar o *ServiceState* para reparos automáticos de instância. Depois que o conjunto de dimensionamento for aceito no recurso de reparo automático, você poderá usar este cmdlet para suspender ou retomar os reparos automáticos para o conjunto de dimensionamento.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Solucionar problemas

**Falha ao habilitar a política de reparos automáticos**

Se você receber um erro ' BadRequest ' com uma mensagem informando "não foi possível encontrar o membro ' automaticRepairsPolicy ' no objeto do tipo ' Properties '", verifique a versão da API usada para o conjunto de dimensionamento de máquinas virtuais. A versão de API 2018-10-01 ou superior é necessária para este recurso.

**Instância não sendo reparada mesmo quando a política está habilitada**

A instância pode estar no período de carência. Essa é a quantidade de tempo de espera após qualquer alteração de estado na instância antes de executar reparos. Isso é para evitar reparos prematuros ou acidentais. A ação de reparo deve ocorrer depois que o período de carência for concluído para a instância.

**Exibindo o status de integridade do aplicativo para instâncias do conjunto de dimensionamento**

Você pode usar a [API de exibição obter instância](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) para instâncias em um conjunto de dimensionamento de máquinas virtuais para exibir o status de integridade do aplicativo. Com Azure PowerShell, você pode usar o cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) com o sinalizador *-InstanceView* . O status de integridade do aplicativo é fornecido sob a propriedade *vmHealth*.

No portal do Azure, você também pode ver o status de integridade. Vá para um conjunto de dimensionamento existente, selecione **instâncias** no menu à esquerda e examine a coluna **estado de integridade** do status de integridade de cada instância do conjunto de dimensionamento. 

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar a [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as [investigações de integridade do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md) para seus conjuntos de dimensionamento.
