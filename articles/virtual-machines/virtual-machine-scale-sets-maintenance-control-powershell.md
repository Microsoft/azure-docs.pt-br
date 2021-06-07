---
title: Controle de manutenção para atualizações de imagem do so em conjuntos de dimensionamento de máquinas virtuais do Azure usando o PowerShell
description: Saiba como controlar quando as atualizações automáticas de imagem do sistema operacional são distribuídas para os conjuntos de dimensionamento de máquinas virtuais do Azure usando o controle de manutenção e o PowerShell.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91974831"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Visualização: controle de manutenção para atualizações de imagem do so em conjuntos de dimensionamento de máquinas virtuais do Azure usando o PowerShell

O controle de manutenção permite que você decida quando aplicar atualizações automáticas de imagem do sistema operacional convidado a seus conjuntos de dimensionamento de máquinas virtuais. Este tópico aborda as opções de Azure PowerShell para o controle de manutenção. Para obter mais informações sobre como usar o controle de manutenção, consulte [controle de manutenção para conjuntos de dimensionamento de máquinas virtuais do Azure](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> O controle de manutenção para atualizações de imagem do so em conjuntos de dimensionamento de máquinas virtuais do Azure está atualmente em visualização pública.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


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

## <a name="connect-to-an-azure-account"></a>Conectar-se a uma conta do Azure

Conecte-se à sua conta do Azure desejada usando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e [set-AzAccount](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Crie um grupo de recursos como um contêiner para sua configuração. Neste exemplo, um grupo de recursos chamado *myMaintenanceRG* é criado em *eastus2*. Se você já tiver um grupo de recursos que deseja usar, poderá ignorar essa parte. Basta substituir o nome do grupo de recursos por seu próprio no restante dos exemplos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Use [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myconfig* com escopo na imagem do sistema operacional. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> A **duração** da manutenção deve ser de *5 horas* ou mais. A **recorrência** da manutenção deve ser definida como *dia*.

O uso de `-MaintenanceScope OSImage` garante que a configuração de manutenção seja usada para controlar atualizações para o SO convidado.

Se você tentar criar uma configuração com o mesmo nome, mas em um local diferente, obterá um erro. Os nomes de configuração devem ser exclusivos para seu grupo de recursos.

Você pode consultar as configurações de manutenção disponíveis usando [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Associar o conjunto de dimensionamento de máquinas virtuais à configuração de manutenção

Um conjunto de dimensionamento de máquinas virtuais pode ser associado a qualquer configuração de manutenção, independentemente da região e da assinatura da configuração de manutenção. Ao optar pela configuração de manutenção, novas atualizações de imagem do sistema operacional para o conjunto de dimensionamento serão agendadas automaticamente na próxima janela de manutenção disponível.

Use [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) para associar o conjunto de dimensionamento de máquinas virtuais à configuração de manutenção.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Habilitar atualização automática do sistema operacional

Você pode habilitar atualizações automáticas do sistema operacional para cada conjunto de dimensionamento de máquinas virtuais que vai usar o controle de manutenção. Consulte documentar o documento de [escala de máquina virtual do Azure atualizações automáticas de imagem do sistema operacional](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) para habilitar atualizações automáticas do so no conjunto de dimensionamento de máquinas virtuais. 


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre manutenção e atualizações para máquinas virtuais em execução no Azure.

> [!div class="nextstepaction"]
> [Manutenção e atualizações](maintenance-and-updates.md)