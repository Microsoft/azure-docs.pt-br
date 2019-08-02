---
title: Tutorial - Criar VMs que executam uma pilha do SQL, do IIS e do .NET no Azure | Microsoft Docs
description: Neste tutorial, você aprende a instalar a pilha Azure SQL, IIS, .NET em uma máquina virtual Windows no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 88a7a1ea736a418f4b08a22b3fa7b45ab0e126ff
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708030"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Tutorial: Instalar a pilha do SQL, do IIS, do .NET em uma VM do Windows com o Azure PowerShell

Neste tutorial, vamos instalar uma pilha do SQL, do IIS, do .NET usando o Azure PowerShell. Essa pilha consiste em duas VMs que executam o Windows Server 2016, uma com o IIS e o .NET e a outra com o SQL Server.

> [!div class="checklist"]
> * Criar uma máquina virtual 
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM que executa o SQL Server
> * Instalar a extensão do SQL Server

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-an-iis-vm"></a>Criar uma VM do IIS 

Neste exemplo, usamos o cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) no PowerShell Cloud Shell para criar rapidamente uma VM do Windows Server 2016 e, então, instalar o IIS e o .NET Framework. As VMs do IIS e do SQL compartilham um grupo de recursos e a rede virtual e assim criamos variáveis para esses nomes.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Instale o IIS e o .NET Framework usando a extensão de script personalizado com o cmdlet [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Criar outra sub-rede

Crie uma segunda sub-rede para a VM SQL. Obtenha a vNet usando [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Crie uma configuração para a sub-rede usando [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Atualize a vNet com as informações da nova sub-rede usando [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>VM de SQL do Azure

Use uma imagem pré-configurada do Azure marketplace de um servidor SQL para criar a VM SQL. Primeiro criamos a VM, em seguida, instalamos a Extensão do SQL Server na VM. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Use [Set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension) para adicionar a [extensão do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) à VM do SQL.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você instalou uma pilha SQL&#92;IIS&#92;.NET usando o Azure PowerShell. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma máquina virtual 
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM que executa o SQL Server
> * Instalar a extensão do SQL Server

Vá para o próximo tutorial para saber como proteger um servidor Web do IIS com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web do IIS com certificados SSL](tutorial-secure-web-server.md)

