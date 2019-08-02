---
title: Tutorial – Instalar aplicativos em uma VM Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar a Extensão de Script Personalizado para executar scripts e implantar aplicativos em máquinas virtuais do Windows no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 72489c6d0b03166fcb2a5f1ed39f7b8d5408ff24
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708157"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Tutorial – Implantar aplicativos em uma máquina virtual do Windows no Azure com a Extensão de Script Personalizado

Para configurar VMs (máquinas virtuais) de maneira rápida e consistente, é possível usar a [Extensão de script personalizada para Windows](extensions-customscript.md). Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que usa a Extensão de Script Personalizado
> * Exibir um site do IIS em execução depois que a extensão é aplicada

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="custom-script-extension-overview"></a>Visão geral da extensão de script personalizado
A extensão de script personalizado baixa e executa scripts em VMs do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão.

A extensão de script personalizado se integra com modelos do Azure Resource Manager e também pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Usar a Extensão de script personalizado com VMs do Linux e Windows.


## <a name="create-virtual-machine"></a>Criar máquina virtual
Defina o nome de usuário e a senha do administrador para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora você pode criar a VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVM* na localização *EastUs*. Se ainda não existirem, o grupo de recursos *myResourceGroupAutomate* e recursos de rede de suporte são criados. Para permitir o tráfego da web, o cmdlet também abre a porta *80*.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Demora alguns minutos para que os recursos e a VM sejam criados.


## <a name="automate-iis-install"></a>Automatizar a instalação do IIS
Use [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) para instalar a extensão de script personalizado. A extensão executa `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web do IIS e, em seguida, atualiza a página *Default.htm* para mostrar o nome do host da VM:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testar o site
Obtenha o endereço IP público do balanceador de carga com [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). O exemplo a seguir obtém o endereço IP para *myPublicIPAddress* criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Você pode inserir o endereço IP público em um navegador da Web. O site é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego como no exemplo a seguir:

![Site do IIS em execução](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você automatizou a instalação do IIS em uma VM. Você aprendeu como:

> [!div class="checklist"]
> * Usar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que usa a Extensão de Script Personalizado
> * Exibir um site do IIS em execução depois que a extensão é aplicada

Vá para o próximo tutorial para aprender a gerenciar imagens de VM.

> [!div class="nextstepaction"]
> [Criar imagens de VM personalizada](./tutorial-custom-images.md)
