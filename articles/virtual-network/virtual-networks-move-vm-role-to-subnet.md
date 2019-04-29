---
title: Mover uma VM (Clássico) ou uma instância de função de Serviços de Nuvem para uma sub-rede diferente - Azure PowerShell | Microsoft Docs
description: Aprenda a mover VMs (Clássico) e instâncias de função de Serviços de Nuvem para uma sub-rede diferente usando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 787a50a0cbf16089cd15f922b494cd12d680cb43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640388"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Mover uma VM (Clássico) ou uma instância de função de Serviços de Nuvem para uma sub-rede diferente usando o PowerShell
Você pode usar o PowerShell para mover suas VMs (Clássico) de uma sub-rede para outra na mesma rede virtual (VNet). As instâncias de função podem ser movidas editando o arquivo CSCFG em vez de usar o PowerShell.

> [!NOTE]
> Este artigo explica como mover VMs implantadas somente por meio do modelo de implantação clássico.
> 
> 

Por que transferir VMs para outra sub-rede? A migração de sub-rede é útil quando a sub-rede antiga é muito pequena e não pode ser expandida devido às VMs existentes em execução nessa sub-rede. Nesse caso, você pode criar uma nova sub-rede maior e migrar as máquinas virtuais para a nova sub-rede. Após a conclusão da migração, você pode excluir a sub-rede antiga vazia.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Como mover uma VM para outra sub-rede
Para mover uma VM, execute o cmdlet Set-AzureSubnet PowerShell usando o exemplo abaixo como um modelo. No exemplo a seguir, transferimos TestVM da sub-rede atual para Subnet-2. Não deixe de editar o exemplo para refletir o seu ambiente. Observe que sempre que você executar o cmdlet Update-AzureVM como parte de um procedimento, ele reiniciará a sua VM como parte do processo de atualização.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Se você especificou um IP privado interno estático para a sua VM, terá que desmarcar essa configuração antes de poder mover a máquina virtual para uma nova sub-rede. Nesse caso, use o seguinte:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Para mover uma instância de função para outra sub-rede
Para mover uma instância de função, edite o arquivo CSCFG. No exemplo a seguir, transferimos "Role0" na rede virtual *VNETName* da sub-rede atual para *Subnet-2*. Como a instância de função já foi implantada, você só alterará o nome da sub-rede = Subnet-2. Não deixe de editar o exemplo para refletir o seu ambiente.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
