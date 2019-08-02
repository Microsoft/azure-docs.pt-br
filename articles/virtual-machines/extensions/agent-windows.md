---
title: Visão Geral dos Agente de Máquina Virtual do Azure | Microsoft Docs
description: Visão Geral do Agente de Máquina Virtual do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3de0e7ac20296544f7ca02030056aa60542cb0b0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706182"
---
# <a name="azure-virtual-machine-agent-overview"></a>Visão geral do Agente de Máquina Virtual do Azure
O Agente de VM (Máquina Virtual) do Microsoft Azure é um processo seguro e leve que gerencia a interação da máquina virtual (VM) com o Controlador de Malha do Azure. O Agente de VM tem uma função fundamental na habilitação e execução de extensões de máquina virtual do Azure. Extensões de VM habilitam a configuração de VM pós-implantação, como instalação e configuração de software. Extensões de VM também habilitam os recursos de recuperação como redefinir a senha administrativa de uma VM. Sem o Agente de VM do Azure, não é possível executar extensões da VM.

Este artigo fornece detalhes sobre a instalação, detecção e remoção do Agente de Máquina Virtual do Azure.

## <a name="install-the-vm-agent"></a>Instalar o Agente VM

### <a name="azure-marketplace-image"></a>Imagem do Azure Marketplace

O Agente de VM do Azure é instalado por padrão em qualquer VM Windows implantada de uma imagem do Azure Marketplace. Ao implantar uma imagem do Azure Marketplace do portal, PowerShell, Interface de Linha de Comando ou de um modelo do Azure Resource Manager, o Agente de VM do Azure também é instalado.

O Pacote de Agente de Convidado do Windows é dividido em duas partes:

- Agente de Provisionamento (PA)
- Agente de Convidado do Windows (WinGA)

Para inicializar uma VM, você deve ter o PA instalado na VM, no entanto o WinGA não precisa ser instalado. Na hora da implantação da VM, você pode optar por não instalar o WinGA. O exemplo a seguir mostra como selecionar a opção *provisionVmAgent* com um modelo do Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Se você não tiver os Agentes instalados, você não pode usar alguns dos serviços do Azure, como o Azure Backup ou segurança do Azure. Esses serviços exigem uma extensão para serem instalados. Se você tiver implantado uma VM sem o WinGA, você pode instalar a versão mais recente do agente mais tarde.

### <a name="manual-installation"></a>Instalação manual
O agente de VM do Windows pode ser instalado manualmente com um pacote do Windows Installer. Instalação manual pode ser necessária quando você cria uma imagem VM personalizada que é implantada no Azure. Para instalar manualmente o Agente de VM do Windows, [faça o download do instalador do Agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789).

O Agente de VM pode ser instalado clicando duas vezes no arquivo do Windows Installer. Para uma instalação silenciosa ou autônomo do agente de VM, execute o comando a seguir:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Detectar o Agente de VM

### <a name="powershell"></a>PowerShell

O módulo do Azure Resource Manager PowerShell pode ser usado para recuperar informações sobre as VMs do Azure. Para obter informações sobre uma VM, como o estado de provisionamento para o Agente de VM do Azure, use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

A saída de exemplo condensada a seguir mostra a propriedade *ProvisionVMAgent* aninhada dentro de *OSProfile*. Essa propriedade pode ser usada para determinar se o agente de VM foi implantado na VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

O script a seguir pode ser usado para retornar uma lista concisa de nomes de VM e o estado do Agente de VM:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Detecção Manual

Quando conectado a uma VM do Windows, o Gerenciador de Tarefas pode ser usado para examinar os processos em execução. Para verificar o Agente de VM do Azure, abra o Gerenciador de Tarefas, clique na guia *Detalhes* e procure pelo nome de processo **WindowsAzureGuestAgent.exe**. A presença desse processo indica que o agente de VM está instalado.


## <a name="upgrade-the-vm-agent"></a>Atualizar o Agente de VM
O Agente de VM do Azure para Windows é atualizado automaticamente. Conforme novas VMs são implantadas no Azure, elas receberão o agente de VM mais recente em tempo de provisionamento. Imagens de VM personalizadas devem ser atualizadas manualmente para incluir o novo agente de VM em tempo de criação de imagem.


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões de VM, consulte [Visão geral de recursos e extensões de máquina virtual do Azure](overview.md).
