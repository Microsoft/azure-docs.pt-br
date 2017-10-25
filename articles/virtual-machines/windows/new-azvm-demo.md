---
title: Crie VM do Windows usando o cmdlet New-AzVM no Azure Cloud Shell | Microsoft Docs
description: "Aprenda rapidamente a criar máquinas virtuais Windows com o cmdlet New-AzVM no Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Crie uma máquina virtual do Windows com o New-AzVM (visualização) no Cloud Shell 

O cmdlet New-AzVM (visualização) é uma forma simplificada de criar uma nova VM usando o PowerShell. Este guia fornece detalhes sobre como usar o PowerShell no Azure Cloud Shell, com o cmdlet New-AzVM pré-instalado, para criar uma nova máquina virtual do Azure executando o Windows Server 2016. Depois que a implantação for concluída, nos conectamos ao servidor usando RDP.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Criar a VM

Você pode usar o **New-AzVM** para criar uma VM com padrões inteligentes que incluam usando a imagem do Windows Server 2016 Datacenter do Azure Marketplace. Você pode usar apenas o New-AzVM e ele usará os valores padrão para os nomes de recursos. Neste exemplo, definimos o parâmetro **-Nome** como *myVM*. O cmdlet cria todos os recursos necessários usando *myVM* como o prefixo do nome do recurso. 

Verifique se **PowerShell** está selecionado no Cloud Shell e digite:

```azurepowershell-interactive
New-AzVm -Name myVM
```

Você será solicitado a criar um nome de usuário e uma senha para a VM, que será usada quando você se conectar à máquina virtual neste tópico. A senha deve ter de 12 a 123 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial.

Leva um minuto para criar a máquina virtual e os recursos associados. Quando terminar, você pode ver todos os recursos que foram criados usando o cmdlet [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Após a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

Utilize o comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público da máquina virtual. Anote o endereço IP público.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Em seu computador local, abra um prompt de comando e use o comando **mstsc** para iniciar uma sessão de área de trabalho remota com a nova VM. Substitua o endereço IP pelo &lt;publicIPAdress&gt; da sua máquina virtual. Quando solicitado, insira o nome de usuário e a senha fornecida para a VM quando ela foi criada.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você implantou uma máquina virtual simples usando New-AzVM e, em seguida, conectado a ela via RDP. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)
