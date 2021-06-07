---
title: Aplicar licença do Windows a máquinas virtuais do host de sessão – Azure
description: Descreve como aplicar a licença do Windows para VMs de área de trabalho virtual do Windows.
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88010133"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Aplicar licença do Windows a máquinas virtuais do host de sessão

Os clientes que estão devidamente licenciados para executar cargas de trabalho de área de desktop virtual do Windows estão qualificados a aplicar uma licença do Windows a suas máquinas virtuais do host de sessão e executá-las sem pagar por outra licença. Para obter mais informações, consulte [preços da área de trabalho virtual do Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Maneiras de usar sua licença de área de trabalho virtual do Windows
O licenciamento de área de trabalho virtual do Windows permite que você aplique uma licença a qualquer máquina virtual Windows ou Windows Server registrada como um host de sessão em um pool de hosts e receba conexões de usuário. Essa licença não se aplica a máquinas virtuais que estão sendo executadas como servidores de compartilhamento de arquivos, controladores de domínio e assim por diante.

Há algumas maneiras de usar a licença de área de trabalho virtual do Windows:
- Você pode criar um pool de hosts e suas máquinas virtuais de host de sessão usando a [oferta do Azure Marketplace](./create-host-pools-azure-marketplace.md). As máquinas virtuais criadas dessa maneira automaticamente têm a licença aplicada.
- Você pode criar um pool de hosts e suas máquinas virtuais de host de sessão usando o [modelo de Azure Resource Manager do GitHub](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). As máquinas virtuais criadas dessa maneira automaticamente têm a licença aplicada.
- Você pode aplicar uma licença a uma máquina virtual de host de sessão existente. Para fazer isso, primeiro siga as instruções em [criar um pool de hosts com o PowerShell](./create-host-pools-powershell.md) para criar um pool de hosts e VMs associadas e, em seguida, retorne a este artigo para saber como aplicar a licença.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Aplicar uma licença do Windows a uma VM host de sessão
Verifique se você [instalou e configurou o Azure PowerShell mais recente](/powershell/azure/). Execute o seguinte cmdlet do PowerShell para aplicar a licença do Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verifique se a VM do host de sessão está utilizando o benefício de licenciamento
Depois de implantar sua VM, execute este cmdlet para verificar o tipo de licença:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Uma VM de host de sessão com a licença do Windows aplicada mostrará algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

As VMs sem a licença do Windows aplicada mostrarão algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Execute o seguinte cmdlet para ver uma lista de todas as VMs de host de sessão que têm a licença do Windows aplicada em sua assinatura do Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
