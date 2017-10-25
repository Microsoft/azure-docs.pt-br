---
title: "Visão Geral dos Agente de Máquina Virtual do Azure | Microsoft Docs"
description: "Visão Geral do Agente de Máquina Virtual do Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: nepeters
ms.openlocfilehash: 24ad2c2d2872f844e32d3fae559683c3d992bd00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-machine-agent-overview"></a>Visão geral do Agente de Máquina Virtual do Azure

O AM Agent (Agente de Máquina Virtual do Microsoft Azure) é um processo seguro e leve que gerencia a interação de VM com o Controlador de Malha do Azure. O Agente de VM tem uma função fundamental na habilitação e execução de extensões de máquina virtual do Azure. Extensões de VM habilitam a configuração máquinas virtuais pós-implantação, como instalação e configuração de software. Extensões de máquina virtual também habilitam os recursos de recuperação como redefinir a senha administrativa de uma máquina virtual. Sem o Agente de VM do Azure, não é possível executar extensões da máquina virtual.

Este documento fornece detalhes sobre a instalação, detecção e remoção do Agente de Máquina Virtual do Azure.

## <a name="install-the-vm-agent"></a>Instalar o Agente VM

### <a name="azure-gallery-image"></a>Imagem da galeria do Azure

O Agente de VM do Azure é instalado por padrão em qualquer máquina virtual Windows implantada de uma imagem da Galeria do Azure. Ao implantar uma imagem da Galeria do Azure do Portal, PowerShell, Interface de Linha de Comando ou de um modelo do Azure Resource Manager, o Agente de VM do Azure também é instalado. 

### <a name="manual-installation"></a>Instalação manual

O agente de VM do Windows pode ser instalado manualmente usando um pacote do Windows Installer. A instalação manual pode ser necessária ao criar uma imagem de máquina virtual personalizada que será implantada no Azure. Para instalar o Agente de VM do Windows manualmente, baixe o instalador do Agente de VM deste local [Download do Agente de VM do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=394789). 

O Agente de VM pode ser instalado clicando duas vezes no arquivo do Windows Installer. Para uma instalação silenciosa ou autônomo do agente de VM, execute o comando a seguir.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Detectar o Agente de VM

### <a name="powershell"></a>PowerShell

O módulo do Azure Resource Manager PowerShell pode ser usado para recuperar informações sobre as Máquinas Virtuais do Azure. Executar `Get-AzureRmVM` retorna bastante informação, incluindo o estado de provisionamento do Agente de VM do Azure.

```PowerShell
Get-AzureRmVM
```

Este é apenas um subconjunto da saída do `Get-AzureRmVM`. Observe a propriedade `ProvisionVMAgent` aninhada em `OSProfile`, que poderá ser usada para determinar se o agente de VM foi implantado na máquina virtual.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

O script a seguir pode ser usado para retornar uma lista concisa de nomes de máquina virtual e o estado do Agente de VM.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Detecção Manual

Quando conectado a uma VM do Microsoft Azure, o gerenciador de tarefas pode ser usado para examinar os processos em execução. Para verificar o Agente de VM do Azure, abra o Gerenciador de Tarefas > clique na guia Detalhes e procure pelo nome de processo `WindowsAzureGuestAgent.exe`. A presença desse processo indica que o agente de VM está instalado.

## <a name="upgrade-the-vm-agent"></a>Atualizar o Agente de VM

O Agente de VM do Azure para Windows é atualizado automaticamente. Conforme novas máquinas virtuais são implantadas no Azure, elas receberão o agente de VM mais recente. Imagens de VM personalizadas devem ser atualizadas manualmente para incluir o novo agente de VM.