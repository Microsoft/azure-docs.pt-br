---
title: Redefinir os Serviços de Área de Trabalho Remota ou a senha de administrador em uma VM do Windows | Microsoft Docs
description: Saiba como redefinir uma senha de conta ou Serviços de Área de Trabalho Remota em uma VM do Windows usando o portal do Azure ou o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 0a12cbabc28640283f5a28eb7a83c7d7717e0882
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921197"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Redefinir os Serviços de Área de Trabalho Remota ou a senha de administrador em uma VM do Windows
Se você não conseguir conectar-se a uma VM (máquina virtual) do Windows, poderá redefinir a senha de administrador local ou redefinir a configuração dos Serviços de Área de Trabalho Remota (sem suporte nos controladores de domínio do Windows). Para redefinir a senha, use o portal do Azure ou a extensão de acesso da VM no Azure PowerShell. Depois de entrar na VM, redefina a senha desse administrador local.  
Se você estiver usando o PowerShell, verifique se tem o [último módulo do PowerShell instalado e configurado](/powershell/azure/overview) e se está conectado à sua assinatura do Azure. Você também pode [realizar essas etapas para VMs criadas com o modelo de implantação clássico](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

É possível redefinir os Serviços de Área de Trabalho Remota e as credenciais, conforme a seguir:

- [Redefinir usando o portal do Azure](#reset-by-using-the-azure-portal)

- [Redefinir usando a extensão de VMAccess e o PowerShell ](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Redefinir usando o portal do Azure

Primeiro, entre no [Portal do Azure](https://portal.azure.com) e selecione **Máquinas Virtuais** no menu à esquerda. 

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta de administrador local**

1. Selecione a VM do Windows e, em seguida, selecione **Redefinir senha** em **Suporte + Solução de problemas**. A janela **Redefinir senha** é exibida.

2. Selecione **Redefinir senha**, insira um nome de usuário e uma senha e, em seguida, selecione **Atualizar**. 

3. Tente se conectar à VM novamente.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração dos Serviços de Área de Trabalho Remota**

Esse processo será habilitar o serviço de área de trabalho remota na VM e criar uma regra de firewall para a porta do RDP padrão 3389.

1. Selecione a VM do Windows e, em seguida, selecione **Redefinir senha** em **Suporte + Solução de problemas**. A janela **Redefinir senha** é exibida. 

2. Selecione **Redefinir somente a configuração** e, em seguida, selecione **Atualizar**. 

3. Tente se conectar à VM novamente.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Redefinir usando a extensão de VMAccess e o PowerShell

Primeiro, verifique se você tem o [módulo do PowerShell mais recente instalado e configurado](/powershell/azure/overview), e está conectado à assinatura do Azure usando o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta de administrador local**

- Redefina a senha ou o nome de usuário do administrador com o cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) do PowerShell. A configuração `typeHandlerVersion` deve ser 2.0 ou superior, porque a versão 1 está preterida. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Se você inserir um nome diferente daquele da conta atual do administrador local na VM, a extensão de VMAccess adicionará a conta do administrador local com esse nome e atribuirá a senha especificada a essa conta. Se a conta do administrador local na VM existir, a extensão de VMAccess redefinirá a senha. Se a conta estiver desabilitada, a extensão de VMAccess a habilitará.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração dos Serviços de Área de Trabalho Remota**

1. Redefina o acesso remoto à VM com o cmdlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) do PowerShell. O seguinte exemplo redefine a extensão de acesso chamada `myVMAccess` na VM chamada `myVM` no grupo de recursos `myResourceGroup`:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > A qualquer momento, uma VM pode ter apenas um único agente de acesso de VM. Para definir as propriedades do agente de acesso da VM, use a opção `-ForceRerun`. Ao usar `-ForceRerun`, assegure-se de usar o mesmo nome do agente de acesso à VM que você pode ter usado em qualquer comando anterior.

1. Se você ainda não conseguir conectar-se remotamente à máquina virtual, consulte [Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se você perder a conexão com o controlador de domínio do Windows, será necessário restaurá-la a partir de um backup do controlador de domínio.

## <a name="next-steps"></a>Próximas etapas

- Se a extensão de acesso à VM do Azure não responder e você não conseguir redefinir a senha, será possível [redefinir a senha do Windows local offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esse método é mais avançado e exige que você conecte o disco rígido virtual da VM problemática a outra VM. Primeiro siga as etapas documentadas neste artigo e somente se essas etapas não funcionarem tente o método de redefinição de senha offline.

- [Saiba mais sobre os recursos e extensões de VM do Azure](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Conecte-se a uma máquina virtual do Azure com RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Resolva as conexões da Área de Trabalho Remota com uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

