---
title: Criar uma VM Windows de um modelo no Azure | Microsoft Docs
description: Use um modelo do Resource Manager e o PowerShell para criar facilmente uma nova VM do Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70a9680b9f8696cd40cb5631217861dc6d1d7ad8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719906"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Criar uma máquina virtual Windows usando um modelo do Resource Manager

Saiba como criar uma máquina virtual do Windows usando um modelo do Resource Manager e o Azure PowerShell do Azure Cloud shell. O modelo usado neste artigo implanta uma única máquina virtual executando o Windows Server em uma nova rede virtual com uma única sub-rede. Para criar uma máquina virtual Linux, confira [como criar uma máquina virtual Linux com modelos do Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A criação de uma máquina virtual do Azure geralmente inclui duas etapas:

- Crie um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual.
- Crie uma máquina virtual.

O exemplo a seguir cria uma VM de um [modelo de início rápido do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Veja uma cópia do modelo:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Para executar o script do PowerShell, selecione **Experimente** para abrir o Azure Cloud shell. Para colar o script, o shell e, em seguida, selecione **colar**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Se você optar por instalar e usar o PowerShell localmente, em vez do Azure Cloud shell, este tutorial requer o módulo do PowerShell do Azure. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

No exemplo anterior, você especificou um modelo armazenado no GitHub. Também é possível baixar ou criar um modelo e especificar o caminho local com o parâmetro `--template-file`.

Estes são alguns recursos adicionais:

- Para saber como desenvolver modelos do Resource Manager, confira a [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- Para ver os esquemas de máquina virtual do Azure, consulte [referência de modelo do Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais exemplos de modelo de máquina virtual, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

O último comando do PowerShell do script anterior mostra o nome da máquina virtual. Para se conectar à máquina virtual, consulte [como conectar-se e entrar uma máquina virtual do Azure executando Windows](./connect-logon.md).

## <a name="next-steps"></a>Próximas etapas

- Se houver problemas com a implantação, confira [Solução de erros de implantação comuns do Azure com o Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Saiba como criar e gerenciar uma máquina virtual em [Criar e gerenciar VMs Windows com o módulo do Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Confira a sintaxe e as propriedades do JSON para os tipos de recursos que você implantou para saber mais sobre a criação de modelos:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
