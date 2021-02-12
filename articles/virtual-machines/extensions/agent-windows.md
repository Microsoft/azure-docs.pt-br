---
title: Visão Geral do Agente de Máquina Virtual do Azure
description: Visão Geral do Agente de Máquina Virtual do Azure
services: virtual-machines-windows
ms.subservice: extensions
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 07/20/2019
ms.author: mimckitt
ms.openlocfilehash: 3724b8a2afb89594c73f7dae782658ec8978963a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016464"
---
# <a name="azure-virtual-machine-agent-overview"></a>Visão geral do Agente de Máquina Virtual do Azure
O Agente de VM (Máquina Virtual) do Microsoft Azure é um processo seguro e leve que gerencia a interação da máquina virtual (VM) com o Controlador de Malha do Azure. O Agente de VM tem uma função fundamental na habilitação e execução de extensões de máquina virtual do Azure. Extensões de VM habilitam a configuração de VM pós-implantação, como instalação e configuração de software. Extensões de VM também habilitam os recursos de recuperação como redefinir a senha administrativa de uma VM. Sem o Agente de VM do Azure, não é possível executar extensões da VM.

Este artigo detalha a instalação e a detecção do agente de máquina virtual do Azure.

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
O agente de VM do Windows pode ser instalado manualmente com um pacote do Windows Installer. Instalação manual pode ser necessária quando você cria uma imagem VM personalizada que é implantada no Azure. Para instalar manualmente o Agente de VM do Windows, [faça o download do instalador do Agente de VM](https://go.microsoft.com/fwlink/?LinkID=394789). O agente de VM tem suporte no Windows Server 2008 (64 bits) e posterior.

> [!NOTE]
> É importante atualizar a opção AllowExtensionOperations depois de instalar manualmente o VMAgent em uma VM que foi implantada a partir da imagem sem o ProvisionVMAgent Enable.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Pré-requisitos

- O agente de VM do Windows precisa de pelo menos o Windows Server 2008 SP2 (64 bits) para ser executado, com o .NET Framework 4,0. Consulte [suporte mínimo de versão para agentes de máquina virtual no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

- Verifique se sua VM tem acesso ao endereço IP 168.63.129.16. Para obter mais informações, consulte [o que é o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

- Verifique se o DHCP está habilitado dentro da VM convidada. Isso é necessário para obter o endereço de host ou de malha do DHCP para que o agente de VM IaaS e as extensões funcionem. Se você precisar de um IP privado estático, deverá configurá-lo por meio do portal do Azure ou do PowerShell e certificar-se de que a opção DHCP dentro da VM esteja habilitada. [Saiba mais](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre como configurar um endereço IP estático com o PowerShell.


## <a name="detect-the-vm-agent"></a>Detectar o Agente de VM

### <a name="powershell"></a>PowerShell

O módulo do Azure Resource Manager PowerShell pode ser usado para recuperar informações sobre as VMs do Azure. Para obter informações sobre uma VM, como o estado de provisionamento para o Agente de VM do Azure, use [Get-AzVM](/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

A saída de exemplo condensada a seguir mostra a propriedade *ProvisionVMAgent* aninhada dentro `OSProfile` . Essa propriedade pode ser usada para determinar se o agente de VM foi implantado na VM:

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
O agente de VM do Azure para Windows é atualizado automaticamente em imagens implantadas no Azure Marketplace. Conforme novas VMs são implantadas no Azure, elas receberão o agente de VM mais recente em tempo de provisionamento. Se você tiver instalado o agente manualmente ou estiver implantando imagens de VM personalizadas, será necessário atualizar manualmente para incluir o novo agente de VM no momento da criação da imagem.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Coleção de logs automáticos do agente convidado do Windows
O agente convidado do Windows tem um recurso para coletar automaticamente alguns logs. Esse recurso é o controlador pelo processo de CollectGuestLogs.exe. Ele existe para os serviços de nuvem PaaS e para máquinas virtuais IaaS e seu objetivo é & rapidamente coletar automaticamente alguns logs de diagnóstico de uma VM, para que eles possam ser usados para análise offline. Os logs coletados são logs de eventos, logs do sistema operacional, logs do Azure e algumas chaves do registro. Ele produz um arquivo ZIP que é transferido para o host da VM. Esse arquivo ZIP pode então ser examinado por equipes de engenharia e profissionais de suporte para investigar problemas na solicitação do cliente que possui a VM.

## <a name="guest-agent-and-osprofile-certificates"></a>Agente convidado e certificados OSProfile
O agente de VM do Azure é responsável por instalar os certificados referenciados no `OSProfile` de um conjunto de dimensionamento de máquinas virtuais ou VM. Se você remover manualmente esses certificados do console do MMC certificados dentro da VM convidada, espera-se que o agente convidado os adicione de volta.
Para remover permanentemente um certificado, você precisará removê-lo do `OSProfile` e, em seguida, removê-lo de dentro do sistema operacional convidado.

Para uma máquina virtual, use [Remove-AzVMSecret]() para remover certificados do `OSProfile` .

Para obter mais informações sobre os certificados do conjunto de dimensionamento de máquinas virtuais, consulte [conjuntos de dimensionamento de máquinas virtuais-como fazer remover certificados preteridos?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates)


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões de VM, consulte [Visão geral de recursos e extensões de máquina virtual do Azure](overview.md).