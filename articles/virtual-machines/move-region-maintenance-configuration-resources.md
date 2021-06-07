---
title: Mover recursos associados a uma configuração de manutenção para outra região
description: Saiba como mover os recursos associados a uma configuração de manutenção de VM para outra região do Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 88082c441dafdc7571f2b9775bfc07ebe3ca5aa4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730499"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Mover recursos em uma configuração de controle de manutenção para outra região

Siga este artigo para mover os recursos associados a uma configuração de controle de manutenção para uma região diferente do Azure. Talvez você queira mover uma configuração por vários motivos. Por exemplo, para aproveitar uma nova região, implantar recursos ou serviços disponíveis em uma região específica, para atender aos requisitos internos de políticas e governança, ou em resposta ao planejamento de capacidade.

O [controle de manutenção](maintenance-control.md), com configurações de manutenção personalizadas, permite que você controle como as atualizações de plataforma são aplicadas às VMs e aos hosts dedicados do Azure. Há alguns cenários para mover o controle de manutenção entre regiões:

- Para mover os recursos associados a uma configuração de manutenção, mas não a configuração em si, siga este artigo.
- Para mover sua configuração de controle de manutenção, mas não os recursos associados à configuração, siga [estas instruções](move-region-maintenance-configuration.md).
- Para mover a configuração de manutenção e os recursos associados a ela, primeiro siga [estas instruções](move-region-maintenance-configuration.md). Em seguida, siga as instruções neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover os recursos associados a uma configuração de controle de manutenção:

- Verifique se os recursos que você está movendo existem na nova região antes de começar.
- Verifique as configurações de controle de manutenção associadas às VMs do Azure e aos hosts dedicados do Azure que você deseja mover. Verifique cada recurso individualmente. Atualmente, não há como recuperar configurações para vários recursos.
- Ao recuperar configurações de um recurso:
    - Certifique-se de usar a ID da assinatura para a conta, não uma ID de host dedicada do Azure.
    - CLI: o parâmetro--output Table é usado somente para leitura e pode ser excluído ou alterado.
    - PowerShell: o parâmetro de nome Format-Table é usado somente para leitura e pode ser excluído ou alterado.
    - Se você usar o PowerShell, receberá um erro se tentar listar as configurações de um recurso que não tem nenhuma configuração associada. O erro será semelhante a: "falha na operação com o status: ' não encontrado '. Detalhes: 404 erro de cliente: não encontrado para a URL ".

    
## <a name="prepare-to-move"></a>Preparar para mover

1. Antes de começar, defina essas variáveis. Fornecemos um exemplo para cada um.

    **Variável** | **Detalhes** | **Exemplo**
    --- | ---
    $subId | ID da assinatura que contém as configurações de manutenção | "nossa-Subscription-ID"
    $rsrcGroupName | Nome do grupo de recursos (VM do Azure) | "VMResourceGroup"
    $vmName | Nome do recurso da VM |  MyVM
    $adhRsrcGroupName |  Grupo de recursos (hosts dedicados) | "HostResourceGroup"
    $adh | Nome de host dedicado | "myhost"
    $adhParentName | Nome do recurso pai | Hosts
    
2. Para recuperar as configurações de manutenção usando o comando [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) do PowerShell:

    - Para hosts dedicados do Azure, execute:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Para VMs do Azure, execute:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Para recuperar as configurações de manutenção usando o comando de [atribuição de manutenção de AZ](/cli/azure/ext/maintenance/maintenance/assignment) da CLI:

    - Para hosts dedicados do Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Para VMs do Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Mover 

1. [Siga estas instruções](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) para mover as VMs do Azure para a nova região.
2. Depois que os recursos forem movidos, reaplique as configurações de manutenção aos recursos na nova região, conforme apropriado, dependendo se você moveu as configurações de manutenção. Você pode aplicar uma configuração de manutenção a um recurso usando o [PowerShell](../virtual-machines/maintenance-control-powershell.md) ou a [CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Verificar a movimentação

Verifique os recursos na nova região e verifique as configurações associadas para os recursos na nova região. 

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Após a movimentação, considere excluir os recursos movidos na região de origem.


## <a name="next-steps"></a>Próximas etapas

Siga [estas instruções](move-region-maintenance-configuration.md) se você precisar mover as configurações de manutenção. 
