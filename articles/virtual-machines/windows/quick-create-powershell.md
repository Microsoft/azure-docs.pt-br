---
title: Início Rápido – Criar uma VM Windows com o Azure PowerShell | Microsoft Docs
description: Neste início rápido, você aprende a usar o Azure PowerShell para criar uma máquina virtual Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2c176ec9b7a2216eeb61e3f8fd4ef4b182b255a1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722914"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Início Rápido: criar uma máquina virtual do Windows no Azure com o PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts. Este início rápido mostra como usar o módulo do Azure PowerShell para implantar uma VM (máquina virtual) no Azure que executa o Windows Server 2016. Você também habilitará o protocolo RDP para a VM e instalará o servidor Web do IIS para mostrar a VM em ação.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.


## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Forneça nomes para cada um dos recursos e o cmdlet `New-AzVM` os criará, caso ainda não existam.

Quando solicitado, forneça um nome de usuário e uma senha a serem usados como credenciais de entrada para a VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após a conclusão da implantação, habilite o protocolo RDP na VM. Para ver a VM em ação, o servidor Web do IIS é então instalado.

Para ver o endereço IP público da VM, use o cmdlet [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress):

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Use o comando a seguir para criar uma sessão de área de trabalho remota no computador local. Substitua o endereço IP pelo endereço IP público da VM. 

```powershell
mstsc /v:publicIpAddress
```

Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite o nome do usuário como **localhost**\\*nomedeusuário*, insira a senha que você criou para a máquina virtual e, sem seguida, clique em **OK**.

Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para criar a conexão

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do IIS. Abra um prompt do PowerShell na VM e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Quando terminar, feche a conexão RDP com a VM.

## <a name="view-the-web-server-in-action"></a>Ver o servidor web em ação

Com o IIS instalado e a porta 80 agora aberta na VM pela Internet, use um navegador da Web de sua escolha para exibir a página inicial padrão do IIS. Use o endereço IP público da VM obtido em uma etapa anterior. O seguinte exemplo mostra o site padrão do IIS:

![Site do IIS padrão](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, você poderá usar o cmdlet [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, abriu uma porta de rede para o tráfego da Web e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)
