---
title: "Redefinir a senha ou a configuração da Área de Trabalho Remota em uma VM Windows no Azure | Microsoft Docs"
description: "Saiba como redefinir uma senha de conta ou os serviços de Área de Trabalho Remota em uma VM Windows criada com o modelo de implantação Clássico usando o portal do Azure ou o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 43e5cf1ab3bc3121d7e3915ea0785998e0ee2fc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Como redefinir o serviço de Área de Trabalho Remota ou sua senha de logon em uma VM Windows criada com o modelo de implantação Clássico
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Você também pode [realizar essas etapas para VMs criadas com o modelo de implantação do Resource Manager](../reset-rdp.md).

Se não conseguir se conectar a uma máquina virtual do Windows (VM), você pode redefinir a senha de administrador local ou a configuração do serviço de Área de Trabalho Remota. Você pode usar o Portal do Azure ou a extensão VM Access no Azure PowerShell para redefinir a senha.

## <a name="ways-to-reset-configuration-or-credentials"></a>Maneiras para redefinir a configuração ou as credenciais
É possível redefinir os serviços e as credenciais de Área de Trabalho Remota de várias maneiras diferentes, dependendo de suas necessidades:

- [Redefinir usando o portal do Azure](#azure-portal)
- [Redefinir usando o Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portal do Azure
É possível usar o [portal do Azure](https://portal.azure.com) para redefinir o serviço de Área de Trabalho Remota. Para expandir o menu do portal, clique nas três barras no canto superior esquerdo e clique em **Máquinas virtuais (clássico)**:

![Navegue até sua VM do Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Selecione sua máquina virtual do Windows e clique em **Redefinir Remota...** . A caixa de diálogo a seguir aparece para redefinir a configuração da Área de Trabalho Remota:

![Página Redefinir configuração de RDP](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Você também pode redefinir o nome e a senha da conta de administrador local. Em sua VM, clique em **Suporte + Solução de Problemas** > **Redefinir senha**. A folha de redefinição de senha é exibida:

![Página Redefinir senha](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Depois de inserir o novo nome de usuário e a senha, clique em **Salvar**.

## <a name="vmaccess-extension-and-powershell"></a>Extensão VMAccess e PowerShell
Verifique se o Agente da VM está instalado na máquina virtual. A extensão VMAccess não precisa ser instalada para que você possa usá-la, desde que o Agente da VM esteja disponível. Verifique se o Agente da VM já está instalado usando o comando a seguir. (Substitua "myCloudService" e "myVM" pelos nomes do seu serviço de nuvem e da sua VM, respectivamente. É possível conhecer esses nomes executando `Get-AzureVM` sem nenhum parâmetro).

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele exibir **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Se você criou a máquina virtual pelo uso do portal, verifique se `$vm.GetInstance().ProvisionGuestAgent` retorna **True**. Caso contrário, você pode defini-lo usando esse comando:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Este comando impedirá o erro a seguir ao executar o comando **Set-AzureVMExtension** nas etapas a seguir: “O Agente Convidado de Provisionamento deve estar habilitado no objeto da VM antes de configurar a Extensão de Acesso à VM IaaS.”

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta de administrador local**
Crie uma credencial de entrada com o nome da conta de administrador local atual e uma nova senha, em seguida, execute `Set-AzureVMAccessExtension` como a seguir.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Se você digitar um nome diferente daquele da conta atual, a extensão VMAccess renomeará a conta de administrador local, atribuirá a senha a essa conta e emitirá uma saída da Área de Trabalho Remota. Se a conta de administrador local estiver desabilitada, a extensão VMAccess a habilita.

Esses comandos também redefinem a configuração do serviço da Área de Trabalho Remota.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Redefinir a configuração dos serviços de Área de Trabalho Remota**
Para redefinir a configuração de serviço da Área de Trabalho Remota, execute o comando a seguir:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

A extensão VMAccess executa dois comandos na máquina virtual:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Este comando habilita o grupo interno do Firewall do Windows que permite o tráfego de entrada da Área de Trabalho Remota, que usa a porta TCP 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Este comando define o valor de Registro fDenyTSConnections como 0, habilitando as conexões de Área de Trabalho Remota.

## <a name="next-steps"></a>Próximas etapas
Se a extensão de acesso a VM do Azure não responder e não for possível redefinir a senha, você pode [redefinir a senha local do Windows offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esse método é um processo mais avançado e exige que você conecte o disco rígido virtual da VM problemática em outra VM. Siga as etapas documentadas neste artigo primeiro; e só tente o método de redefinição de senha offline como último recurso.

[Recursos e extensões de VM do Azure](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Conectar-se a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

