---
title: Controle de manutenção para máquinas virtuais do Azure usando o PowerShell
description: Saiba como controlar quando a manutenção é aplicada às suas VMs do Azure usando o controle de manutenção e o PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 2cc935e81e867609159b5c150b6ee7c346bb9f8e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026141"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Controlar atualizações com controle de manutenção e Azure PowerShell

O controle de manutenção permite que você decida quando aplicar atualizações de plataforma à infraestrutura de host de suas VMs isoladas e hosts dedicados do Azure. Este tópico aborda as opções de Azure PowerShell para o controle de manutenção. Para obter mais informações sobre os benefícios de usar o controle de manutenção, suas limitações e outras opções de gerenciamento, consulte [gerenciando atualizações de plataforma com o controle de manutenção](maintenance-control.md).
 
## <a name="enable-the-powershell-module"></a>Habilitar o módulo do PowerShell

Lembre-se de que o `PowerShellGet` está atualizado.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Instale o `Az.Maintenance` módulo do PowerShell.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Se você estiver instalando localmente, abra o prompt do PowerShell como administrador.

Você também pode ser solicitado a confirmar que deseja instalar a partir de um *repositório não confiável*. Digite `Y` ou selecione **Sim para todos** para instalar o módulo.


## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Crie um grupo de recursos como um contêiner para sua configuração. Neste exemplo, um grupo de recursos chamado *myMaintenanceRG* é criado em *lesteus*. Se você já tiver um grupo de recursos que deseja usar, poderá ignorar essa parte e substituir o nome do grupo de recursos por sua conta no restante dos exemplos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Use [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myconfig* com escopo para o host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

O uso de `-MaintenanceScope host` garante que a configuração de manutenção seja usada para controlar atualizações no host.

Se você tentar criar uma configuração com o mesmo nome, mas em um local diferente, receberá um erro. Os nomes de configuração devem ser exclusivos para seu grupo de recursos.

Você pode consultar as configurações de manutenção disponíveis usando [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Criar uma configuração de manutenção com a janela agendada

Você também pode declarar uma janela agendada quando o Azure aplicará as atualizações em seus recursos. Este exemplo cria uma configuração de manutenção chamada myconfig com uma janela agendada de 5 horas na quarta segunda-feira de cada mês. Depois de criar uma janela agendada, você não precisa mais aplicar as atualizações manualmente.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> A **duração** da manutenção deve ser de *2 horas* ou mais. A **recorrência** de manutenção deve ser definida para pelo menos ocorrer uma vez em 35 dias.

A **recorrência** da manutenção pode ser expressa como diária, semanal ou mensal. Alguns exemplos são:
 - **Daily**-RecurEvery "Day" **ou** "3Days" 
 - **Weekly**-RecurEvery "3Weeks" **ou** "Week sábado, domingo" 
 - **mensal**-RecurEvery "mês day23, day24" **ou** "mês, último domingo" **ou** "mês quarta-feira"  
      

## <a name="assign-the-configuration"></a>Atribuir a configuração

Use [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) para atribuir a configuração à VM isolada ou ao host dedicado do Azure.

### <a name="isolated-vm"></a>VM isolada

Aplique a configuração a uma VM usando a ID da configuração. Especifique `-ResourceType VirtualMachines` e forneça o nome da VM para `-ResourceName` e o grupo de recursos da VM para `-ResourceGroupName` . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Host dedicado

Para aplicar uma configuração a um host dedicado, você também precisa incluir `-ResourceType hosts` , `-ResourceParentName` com o nome do grupo de hosts e `-ResourceParentType hostGroups` . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Verificar se há atualizações pendentes

Use [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) para ver se há atualizações pendentes. Use `-subscription` para especificar a assinatura do Azure da VM se ela for diferente da que você fez logon.

Se não houver nenhuma atualização para mostrar, esse comando não retornará nada. Caso contrário, retornará um objeto PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>VM isolada

Verifique se há atualizações pendentes para uma VM isolada. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Host dedicado

Para verificar se há atualizações pendentes para um host dedicado. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura. Substitua os valores dos recursos pelos seus próprios.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Aplicar atualizações

Use [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) para aplicar atualizações pendentes.

### <a name="isolated-vm"></a>VM isolada

Crie uma solicitação para aplicar atualizações a uma VM isolada.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Em caso de êxito, esse comando retornará um `PSApplyUpdate` objeto. Você pode usar o atributo Name no `Get-AzApplyUpdate` comando para verificar o status da atualização. Consulte [verificar status da atualização](#check-update-status).

### <a name="dedicated-host"></a>Host dedicado

Aplicar atualizações a um host dedicado.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Verificar status da atualização
Use [Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) para verificar o status de uma atualização. Os comandos mostrados abaixo mostram o status da atualização mais recente usando `default` o para o `-ApplyUpdateName` parâmetro. Você pode substituir o nome da atualização (retornado pelo comando [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) ) para obter o status de uma atualização específica.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime será a hora em que a atualização foi concluída, iniciada por você ou pela plataforma caso a janela de automanutenção não tenha sido usada. Se nunca houvesse uma atualização aplicada por meio do controle de manutenção, o valor padrão será exibido.

### <a name="isolated-vm"></a>VM isolada

Verifique se há atualizações para uma máquina virtual específica.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Host dedicado

Verifique se há atualizações para um host dedicado.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Remover uma configuração de manutenção

Use [Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) para excluir uma configuração de manutenção.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte [manutenção e atualizações](maintenance-and-updates.md).
