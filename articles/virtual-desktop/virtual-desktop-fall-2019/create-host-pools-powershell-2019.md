---
title: Criar o pool de hosts da área de trabalho virtual do Windows (clássico) PowerShell-Azure
description: Como criar um pool de hosts na área de trabalho virtual do Windows (clássico) com cmdlets do PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88003554"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>Criar um pool de hosts na área de trabalho virtual do Windows (clássico) com o PowerShell

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../create-host-pools-powershell.md).

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usar o cliente do PowerShell para criar um pool de host

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Execute o seguinte cmdlet para entrar no ambiente da Área de Trabalho Virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, execute este cmdlet para criar um novo pool de host no locatário da Área de Trabalho Virtual do Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o próximo cmdlet para criar um token de registro a fim de autorizar um host de sessão a ingressar no pool de host e salvá-lo em um novo arquivo no computador local. É possível especificar por quanto tempo o token de registro é válido usando o parâmetro -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar usuários do Azure Active Directory ao grupo de aplicativos da área de trabalho padrão para o pool de host.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O cmdlet **Add-RdsAppGroupUser** não dá suporte à adição de grupos de segurança e adiciona apenas um usuário por vez ao grupo de aplicativos. Se você quiser adicionar vários usuários ao grupo de aplicativos, execute novamente o cmdlet com os nomes principais de usuário apropriados.

Execute o cmdlet a seguir para exportar o token de registro para uma variável, que será usada posteriormente em [Registrar as máquinas virtuais no pool de host da Área de Trabalho Virtual do Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para o pool de host

Agora você pode criar uma máquina virtual do Azure que pode ser unida ao seu pool de host da Área de Trabalho Virtual do Windows.

É possível criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual de uma imagem da Galeria do Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual de uma imagem gerenciada](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual de uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Se você estiver implantando uma máquina virtual usando o Windows 7 como o SO host, o processo de criação e implantação será um pouco diferente. Para obter mais detalhes, confira [Implantar uma máquina virtual do Windows 7 na Área de Trabalho Virtual do Windows](deploy-windows-7-virtual-machine.md).

Depois de criar as máquinas virtuais do host da sessão, [aplique uma licença do Windows a uma VM de host da sessão](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar suas máquinas virtuais Windows ou Windows Server sem pagar por outra licença.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparar as máquinas virtuais para instalações do agente de Área de Trabalho Virtual do Windows

Você precisa fazer o seguinte para preparar suas máquinas virtuais antes de instalar os agentes de Área de Trabalho Virtual do Windows e registrar as máquinas virtuais no seu pool de host da Área de Trabalho Virtual do Windows:

- Você precisa ingressar no domínio no computador. Isso permite que os usuários da Área de Trabalho Virtual do Windows sejam mapeados da conta do Azure Active Directory para a conta do Active Directory e tenham acesso permitido à máquina virtual com êxito.
- Você precisará instalar a função de RDSH (Host da Sessão da Área de Trabalho Remota) se a máquina virtual estiver executando um SO Windows Server. A função de RDSH permite que os agentes de Área de Trabalho Virtual do Windows sejam instalados corretamente.

Para ingressar no domínio com êxito, execute as seguintes ações em cada máquina virtual:

1. [Conecte-se à máquina virtual](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie **Painel de Controle** e selecione **Sistema**.
3. Selecione **Nome do computador**, **Alterar configurações** e selecione **Alterar...**
4. Selecione **Domínio** e insira o domínio do Active Directory na rede virtual.
5. Autentique com uma conta de domínio que tenha privilégios para computadores de ingresso no domínio.

    >[!NOTE]
    > Se você estiver unindo suas VMs em um ambiente do Azure Active Directory Domain Services (Azure AD DS), verifique se o usuário de ingresso no domínio também é um membro do [grupo de Administradores do AAD DC](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrar as máquinas virtuais no pool de host da Área de Trabalho Virtual do Windows

Registrar as máquinas virtuais em um pool de host da Área de Trabalho Virtual do Windows é tão simples quanto instalar os agentes de Área de Trabalho Virtual do Windows.

Para registrar os agentes de Área de Trabalho Virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Conecte-se à máquina virtual](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Baixe e instale o Agente de Área de Trabalho Virtual do Windows.
   - Baixe o [Agente de Área de Trabalho Virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades**, selecione **Desbloquear** e, em seguida, selecione **OK**. Isso permite que o sistema confie no instalador.
   - Execute o instalador. Quando o instalador solicitar o token de registro, insira o valor obtido no cmdlet **Export-RdsRegistrationInfo**.
3. Baixe e instale o carregador de inicialização do Agente de Área de Trabalho Virtual do Windows.
   - Baixe o [Carregador de Inicialização do Agente de Área de Trabalho Virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades**, selecione **Desbloquear** e, em seguida, selecione **OK**. Isso permite que o sistema confie no instalador.
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você já criou seu pool de host, pode preenchê-lo com o RemoteApp. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira o tutorial Gerenciar grupos de aplicativos.

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](../manage-app-groups.md)
