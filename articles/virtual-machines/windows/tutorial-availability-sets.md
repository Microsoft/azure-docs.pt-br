---
title: Tutorial – Alta disponibilidade para VMs Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para implantar máquinas virtuais altamente disponíveis em Conjuntos de Disponibilidade
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d9e3fc52b9cdf5126cb8d58778a59b8e8691f80a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708144"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Tutorial: Criar e implantar máquinas virtuais altamente disponíveis com o Azure PowerShell

Neste tutorial, você aprenderá a aumentar a disponibilidade e confiabilidade de suas VMs (máquinas virtuais) usando conjuntos de disponibilidade. Os conjuntos de disponibilidade garantem que as VMs implantadas no Azure sejam distribuídas entre vários nós de hardware isolados em um cluster. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure


## <a name="availability-set-overview"></a>Visão geral do conjunto de disponibilidade

Um conjunto de disponibilidade é uma funcionalidade de agrupamento lógico para isolar recursos de VM uns dos outros quando são implantados. O Azure garante que as VMs colocadas em um conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de hardware ou de software, somente um subconjunto de suas VMs será afetado e a solução geral permanecerá operacional. Os conjuntos de disponibilidade são essenciais para a criação de soluções de nuvem confiáveis.

Vamos considerar uma solução comum baseada em VM na qual você pode ter quatro servidores Web front-end e duas VMs de back-end. Com o Azure, convém definir dois conjuntos de disponibilidade antes de implantar suas VMs: um para a camada Web e outro para a camada traseira. Quando você cria uma nova VM, especifica o conjunto de disponibilidade como um parâmetro. O Azure garante que as VMs estejam isoladas entre vários recursos de hardware físico. Se o hardware físico no qual um dos seus servidores está sendo executado tiver um problema, você saberá que as outras instâncias dos seus servidores continuarão em execução porque estão em um hardware diferente.

Use Conjuntos de Disponibilidade quando você deseja implantar soluções confiáveis baseadas em VM no Azure.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

O hardware em um local é dividido em vários domínios de atualização e domínios de falha. Um **domínios de atualização** é um grupo de VMs e hardware físico subjacente que podem ser reinicializados simultaneamente. As VMs no mesmo **domínio de falha** compartilham armazenamentos comuns, bem como um comutador de rede e fonte de energia comuns.  

É possível criar um conjunto de disponibilidade usando [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). Neste exemplo, o número de domínios de atualização e de falha é *2* e o conjunto de disponibilidade é chamado *myAvailabilitySet*.

Crie um grupos de recursos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Crie um conjunto de disponibilidade gerenciado usando [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) com o parâmetro `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade
As VMs devem ser criadas dentro do conjunto de disponibilidade para assegurar a distribuição correta pelo hardware. Não é possível adicionar uma VM existente a um conjunto de disponibilidade após sua criação. 


Ao criar uma VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), você usa o parâmetro `-AvailabilitySetName` para especificar o nome do conjunto de disponibilidade.

Primeiro, defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie duas VMs com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) no conjunto de disponibilidade.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Demora alguns minutos para criar e configurar ambas as VMs. Quando tiver terminado, você terá duas máquinas virtuais distribuídas entre o hardware subjacente. 

Se você analisar o conjunto de disponibilidade no portal acessando **Grupos de Recursos** > **myResourceGroupAvailability** > **myAvailabilitySet**, verá como as VMs estão distribuídas entre os dois domínios de atualização e de falha.

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Conferir os tamanhos de VM disponíveis 

Você pode adicionar posteriormente outras VMs ao conjunto de disponibilidade, mas você precisa saber quais tamanhos de VM estão disponíveis no hardware. Use [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Verificar o Assistente do Azure 

Também é possível usar o Assistente do Azure para saber mais sobre como melhorar a disponibilidade das suas VMs. O Assistente do Azure analisa a telemetria de uso e de configuração e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a disponibilidade e a segurança de seus recursos do Azure.

Entre no [portal do Azure](https://portal.azure.com), selecione **Todos os serviços** e digite **Assistente**. O painel do Assistente mostra recomendações personalizadas para a assinatura selecionada. Para saber mais, veja [Introdução ao Assistente do Azure](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure

Avance para o próximo tutorial para saber mais sobre conjuntos de disponibilidade de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento da VM](tutorial-create-vmss.md)


