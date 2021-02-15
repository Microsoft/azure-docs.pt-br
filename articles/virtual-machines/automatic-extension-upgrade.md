---
title: Atualização de extensão automática para VMs e conjuntos de dimensionamento no Azure
description: Saiba como habilitar a atualização de extensão automática para suas máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais no Azure.
author: mayanknayar
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: acc014785105d14c3109cfa420f0e9402ca3f534
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416929"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Visualização: atualização de extensão automática para VMs e conjuntos de dimensionamento no Azure

A atualização de extensão automática está disponível em versão prévia para VMs do Azure e conjuntos de dimensionamento de máquinas virtuais do Azure. Quando a atualização de extensão automática está habilitada em uma VM ou em um conjunto de dimensionamento, a extensão é atualizada automaticamente sempre que o Publicador de extensão libera uma nova versão para essa extensão.

 A atualização automática de extensão tem os seguintes recursos:
- Com suporte para VMs do Azure e conjuntos de dimensionamento de máquinas virtuais do Azure. No momento, não há suporte para conjuntos de dimensionamento de máquinas virtuais Service Fabric.
- As atualizações são aplicadas em um modelo de implantação de primeira disponibilidade (detalhado abaixo).
- Quando aplicado a um conjunto de dimensionamento de máquinas virtuais, não mais do que 20% das máquinas virtuais de conjuntos de dimensionamento de máquinas virtuais serão atualizadas em um único lote (sujeito a um mínimo de uma máquina virtual por lote).
- Funciona para todos os tamanhos de VM e para extensões do Windows e Linux.
- Você pode recusar atualizações automáticas a qualquer momento.
- A atualização de extensão automática pode ser habilitada em um conjunto de dimensionamento de máquinas virtuais de qualquer tamanho.
- Cada extensão com suporte é inscrita individualmente e você pode escolher quais extensões serão atualizadas automaticamente.
- Com suporte em todas as regiões de nuvem pública.


> [!IMPORTANT]
> A atualização de extensão automática está atualmente em visualização pública. Um procedimento de aceitação é necessário para usar a funcionalidade de visualização pública descrita abaixo.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Como funciona a atualização de extensão automática?
O processo de atualização de extensão funciona substituindo a versão de extensão existente em uma VM pela nova versão de extensão publicada pelo editor de extensão. A integridade da VM é monitorada após a instalação da nova extensão. Se a VM não estiver em um estado íntegro dentro de 5 minutos após a conclusão da atualização, a nova versão de extensão será revertida para a versão anterior.

Uma falha na atualização da extensão é repetida automaticamente. Uma nova tentativa é feita automaticamente A cada poucos dias, sem a intervenção do usuário.


## <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Processo de atualização para conjuntos de dimensionamento de máquinas virtuais
1. Antes de iniciar o processo de atualização, o orquestrador garantirá que no máximo 20% das VMs em todo o conjunto de dimensionamento não estejam íntegros (por qualquer motivo).

2. O orquestrador de atualização identifica o lote de instâncias de VM a serem atualizadas, com qualquer lote com um máximo de 20% da contagem total de VMs, sujeito a um tamanho de lote mínimo de uma máquina virtual.

3. Para conjuntos de dimensionamento com investigações de integridade de aplicativo configuradas ou extensão de integridade de aplicativos, a atualização aguarda até 5 minutos (ou a configuração de investigação de integridade definida) para que a VM se torne íntegra, antes de prosseguir para atualizar o próximo lote. Se uma VM não recuperar sua integridade após uma atualização, por padrão, a versão de extensão anterior para a VM será reinstalada.

4. O orquestrador de atualização também controla a porcentagem de VMs que se tornam não íntegras após uma atualização. A atualização será interrompida se mais de 20% das instâncias atualizadas se tornarem não íntegras durante o processo de atualização.

O processo acima continua até todas as instâncias no conjunto de dimensionamento serem atualizadas.

O orquestrador de atualização do conjunto de dimensionamento verifica a integridade geral do conjunto de dimensionamento antes de atualizar cada lote. Durante a atualização de um lote, pode haver outras atividades de manutenção planejadas ou não planejadas simultâneas que poderiam afetar a integridade das máquinas virtuais do conjunto de dimensionamento. Nesses casos, se mais de 20% das instâncias do conjunto de dimensionamento se tornarem não íntegros, a atualização do conjunto de dimensionamento será interrompida no final do lote atual.


### <a name="availability-first-updates"></a>Disponibilidade-primeiras atualizações
O modelo de disponibilidade-primeiro para atualizações orquestradas de plataforma garantirá que as configurações de disponibilidade no Azure sejam respeitadas em vários níveis de disponibilidade.

Para um grupo de máquinas virtuais que passa por uma atualização, a plataforma do Azure orquestrará as atualizações:

**Entre regiões:**
- Uma atualização será movida no Azure globalmente de forma em fases para evitar falhas de implantação em todo o Azure.
- Uma "fase" pode constituir uma ou mais regiões e uma atualização se moverá entre as fases somente se as VMs qualificadas em uma fase forem atualizadas com êxito.
- As regiões emparelhadas geograficamente não serão atualizadas simultaneamente e não poderão estar na mesma fase regional.
- O sucesso de uma atualização é medido rastreando a integridade de uma atualização de VM. A integridade da VM é controlada por meio de indicadores de integridade da plataforma para a VM. No caso de conjuntos de dimensionamento de máquinas virtuais, a integridade da VM é controlada por meio de investigações de integridade do aplicativo ou da extensão de integridade do aplicativo, se aplicada ao conjunto de dimensionamento.

**Dentro de uma região:**
- As VMs em diferentes Zonas de Disponibilidade não são atualizadas simultaneamente.
- As VMs únicas que não fazem parte de um conjunto de disponibilidade são agrupadas em uma base de melhor esforço para evitar atualizações simultâneas para todas as VMs em uma assinatura.  

**Em um ' Set ':**
- Todas as VMs em um conjunto de disponibilidade ou conjunto de dimensionamento comum não são atualizadas simultaneamente.  
- As VMs em um conjunto de disponibilidade comum são atualizadas em limites de domínio de atualização e as VMs em vários domínios de atualização não são atualizadas simultaneamente.  
- As VMs em um conjunto de dimensionamento de máquinas virtuais comum são agrupadas em lotes e atualizadas em limites de domínio de atualização.


## <a name="supported-extensions"></a>Extensões com suporte
A visualização da atualização de extensão automática dá suporte às seguintes extensões (e mais são adicionadas periodicamente):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) e [Linux](./extensions/agent-dependency-linux.md)
- [Extensão de integridade do aplicativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows e Linux


## <a name="enabling-preview-access"></a>Habilitando o acesso de visualização
A habilitação da funcionalidade de visualização requer uma aceitação única para o recurso **AutomaticExtensionUpgradePreview** por assinatura, conforme detalhado abaixo.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como habilitar a visualização para sua assinatura:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para habilitar a visualização para sua assinatura.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>CLI do Azure
Use o [registro de recurso AZ](/cli/azure/feature#az-feature-register) para habilitar a visualização para sua assinatura.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Habilitando a atualização de extensão automática
Para habilitar a atualização de extensão automática para uma extensão, você deve garantir que a propriedade *enableAutomaticUpgrade* seja definida como *true* e adicionada a cada definição de extensão individualmente.


### <a name="rest-api-for-virtual-machines"></a>API REST para máquinas virtuais
Para habilitar a atualização de extensão automática para uma extensão (neste exemplo, a extensão de Dependency Agent) em uma VM do Azure, use o seguinte:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>API REST para conjuntos de dimensionamento de máquinas virtuais
Use o seguinte para adicionar a extensão ao modelo do conjunto de dimensionamento:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell para máquinas virtuais
Use o cmdlet [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) :

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell para conjuntos de dimensionamento de máquinas virtuais
Use o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão ao modelo do conjunto de dimensionamento:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Atualize o conjunto de dimensionamento usando [Update-AzVmss](/powershell/module/az.compute/update-azvmss) depois de adicionar a extensão.


### <a name="azure-cli-for-virtual-machines"></a>CLI do Azure para Máquinas Virtuais
Use o cmdlet [AZ VM Extension Set](/cli/azure/vm/extension#az_vm_extension_set) :

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>CLI do Azure para conjuntos de dimensionamento de máquinas virtuais
Use o cmdlet [AZ vmss Extension Set](/cli/azure/vmss/extension#az_vmss_extension_set) para adicionar a extensão ao modelo do conjunto de dimensionamento:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Atualizações de extensão com várias extensões

Um conjunto de dimensionamento de máquinas virtuais ou VM pode ter várias extensões com a atualização de extensão automática habilitada, além de outras extensões sem atualizações de extensão automáticas.  

Se várias atualizações de extensão estiverem disponíveis para uma máquina virtual, as atualizações poderão ser agrupadas em lote. No entanto, cada atualização de extensão é aplicada individualmente em uma máquina virtual. Uma falha em uma extensão não afeta as outras extensões que podem estar sendo atualizadas. Por exemplo, se duas extensões estiverem agendadas para uma atualização e a primeira atualização de extensão falhar, a segunda extensão ainda será atualizada.

As atualizações de extensão automática também podem ser aplicadas quando um conjunto de dimensionamento de máquinas virtuais ou VM tem várias extensões configuradas com o [sequenciamento de extensão](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). O sequenciamento de extensão é aplicável para a primeira implantação da VM, e quaisquer atualizações de extensão subsequentes em uma extensão são aplicadas de forma independente.


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Saiba mais sobre a extensão de integridade do aplicativo](./windows/automatic-vm-guest-patching.md)
