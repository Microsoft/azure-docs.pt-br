---
title: Criar pool de host da Área de Trabalho Virtual do Windows no PowerShell – Azure
description: Como criar um pool de host na Área de Trabalho Virtual do Windows com cmdlets do PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ec900f0537030d3ed0d1c875e8125806159bd51
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251447"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>Criar um pool de hosts da área de trabalho virtual do Windows com o PowerShell

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de host pode ser associado a vários grupos do RemoteApp, um grupo de aplicativos da área de trabalho e vários hosts da sessão.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já seguiu as instruções em [Configurar o módulo do PowerShell](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usar o cliente do PowerShell para criar um pool de host

Execute o seguinte cmdlet para entrar no ambiente da Área de Trabalho Virtual do Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

Esse cmdlet criará o pool de host, o workspace e o grupo de aplicativos da área de trabalho. Além disso, ele vai registrar o grupo de aplicativos da área de trabalho no workspace. Você pode criar um workspace com este cmdlet ou usar um workspace existente.

Execute o próximo cmdlet para criar um token de registro a fim de autorizar um host de sessão a ingressar no pool de host e salvá-lo em um novo arquivo no computador local. É possível especificar por quanto tempo o token de registro é válido usando o parâmetro -ExpirationHours.

>[!NOTE]
>A data de validade do token não pode ser inferior a uma hora nem superior a um mês. Se você definir *-ExpirationTime* fora desse limite, o cmdlet não criará o token.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Por exemplo, se você quiser criar um token que expire em duas horas, execute este cmdlet:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Depois disso, execute este cmdlet para adicionar usuários do Azure Active Directory ao grupo de aplicativos da área de trabalho padrão para o pool de host.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Execute este próximo cmdlet para adicionar grupos de usuários do Azure Active Directory ao grupo de aplicativos da área de trabalho padrão para o pool de host:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Execute o cmdlet a seguir para exportar o token de registro para uma variável, que será usada posteriormente em [Registrar as máquinas virtuais no pool de host da Área de Trabalho Virtual do Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para o pool de host

Agora você pode criar uma máquina virtual do Azure que pode ser unida ao seu pool de host da Área de Trabalho Virtual do Windows.

É possível criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual de uma imagem da Galeria do Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Criar uma máquina virtual de uma imagem gerenciada](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Criar uma máquina virtual de uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Se você estiver implantando uma máquina virtual usando o Windows 7 como o SO host, o processo de criação e implantação será um pouco diferente. Para obter mais detalhes, confira [Implantar uma máquina virtual do Windows 7 na Área de Trabalho Virtual do Windows](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Depois de criar as máquinas virtuais do host da sessão, [aplique uma licença do Windows a uma VM de host da sessão](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar suas máquinas virtuais Windows ou Windows Server sem pagar por outra licença.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparar as máquinas virtuais para instalações do agente de Área de Trabalho Virtual do Windows

Você precisa fazer o seguinte para preparar suas máquinas virtuais antes de instalar os agentes de Área de Trabalho Virtual do Windows e registrar as máquinas virtuais no seu pool de host da Área de Trabalho Virtual do Windows:

- Você precisa ingressar no domínio no computador. Isso permite que os usuários da Área de Trabalho Virtual do Windows sejam mapeados da conta do Azure Active Directory para a conta do Active Directory e tenham acesso permitido à máquina virtual com êxito.
- Você precisará instalar a função de RDSH (Host da Sessão da Área de Trabalho Remota) se a máquina virtual estiver executando um SO Windows Server. A função de RDSH permite que os agentes de Área de Trabalho Virtual do Windows sejam instalados corretamente.

Para ingressar no domínio com êxito, execute as seguintes ações em cada máquina virtual:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie **Painel de Controle** e selecione **Sistema**.
3. Selecione **Nome do computador**, **Alterar configurações** e selecione **Alterar…**
4. Selecione **Domínio** e insira o domínio do Active Directory na rede virtual.
5. Autentique com uma conta de domínio que tenha privilégios para computadores de ingresso no domínio.

    >[!NOTE]
    > Se você estiver unindo suas VMs em um ambiente do Azure Active Directory Domain Services (Azure AD DS), verifique se o usuário de ingresso no domínio também é um membro do [grupo de Administradores do AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

>[!IMPORTANT]
>Recomendamos que você não habilite nenhuma política ou configuração que desabilite Windows Installer. Se você desabilitar Windows Installer, o serviço não poderá instalar atualizações de agente em seus hosts de sessão e os hosts de sessão não funcionarão corretamente.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrar as máquinas virtuais no pool de host da Área de Trabalho Virtual do Windows

Registrar as máquinas virtuais em um pool de host da Área de Trabalho Virtual do Windows é tão simples quanto instalar os agentes de Área de Trabalho Virtual do Windows.

Para registrar os agentes de Área de Trabalho Virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Conecte-se à máquina virtual](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Baixe e instale o agente de Área de Trabalho Virtual do Windows.
   - Baixe o [agente de Área de Trabalho Virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Execute o instalador. Quando o instalador solicitar o token de registro, insira o valor obtido do cmdlet **Get-AzWvdRegistrationInfo** .
3. Baixe e instale o carregador de inicialização do agente de Área de Trabalho Virtual do Windows.
   - Baixe o [carregador de inicialização do agente de Área de Trabalho Virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](../security-center/security-center-just-in-time.md). Também recomendamos que você não atribua suas VMs a um IP público.

## <a name="update-the-agent"></a>Atualizar o agente

Você precisará atualizar o agente se estiver em uma das seguintes situações:

- Você deseja migrar um host de sessão registrado anteriormente para um novo pool de hosts
- O host de sessão não aparece no pool de hosts após uma atualização

Para atualizar o agente:

1. Entre na VM como um administrador.
2. Vá para **Serviços** e, em seguida, pare os processos do **carregador do agente** **RDAgent** e área de trabalho remota.
3. Em seguida, localize o agente e o carregador de MSIs. Eles estarão localizados na pasta **C:\DeployAgent** ou em qualquer local em que você o salvou ao instalá-lo.
4. Localize os seguintes arquivos e desinstale-os:
     
     - Microsoft. RDInfra. RDAgent. Installer-x64-verx. x. x
     - Microsoft. RDInfra. RDAgentBootLoader. Installer-x64

   Para desinstalar esses arquivos, clique com o botão direito do mouse em cada nome de arquivo e selecione **desinstalar**.
5. Opcionalmente, você também pode remover as seguintes configurações do registro:
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. Depois de desinstalar esses itens, isso deve remover todas as associações com o pool de hosts antigo. Se você quiser registrar novamente esse host no serviço, siga as instruções em [registrar as máquinas virtuais no pool de hosts da área de trabalho virtual do Windows](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).


## <a name="next-steps"></a>Próximas etapas

Agora que já criou seu pool de host, você pode preenchê-lo com RemoteApps. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira o tutorial Gerenciar grupos de aplicativos.

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)
