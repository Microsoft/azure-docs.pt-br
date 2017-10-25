---
title: Instalar uma floresta do Active Directory em uma rede virtual do Azure | Microsoft Docs
description: "Um tutorial que explica como criar uma nova floresta do Active Directory em uma máquina virtual (VM) em uma Rede Virtual do Azure."
services: active-directory, virtual-network
keywords: "máquina virtual do active directory, instalar floresta do active directory, vídeos do active directory do azure  "
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
tags: 
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: joflore
ms.openlocfilehash: 0a45a563d8aed45dd30cc76a13b0e197c248be84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Instalar uma nova floresta do Active Directory em uma rede virtual do Azure
Este tópico mostra como criar um novo ambiente do Active Directory do Windows Server em uma rede virtual do Azure em uma máquina virtual (VM) em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Nesse caso, a rede virtual do Azure não está conectada a uma rede local.

Você também pode estar interessado nestes tópicos relacionados:

* Para um vídeo que mostra essas etapas, consulte [Como instalar uma nova floresta do Active Directory em uma rede virtual do Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* Você também pode [configurar uma VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) e então instalar uma nova floresta ou então estender uma floresta local para uma rede virtual do Azure. Para essas etapas, consulte [Instalar um controlador de domínio do Active Directory de réplica em uma rede virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md).
* Para obter diretrizes conceituais sobre como instalar os Serviços de Domínio Active Directory (AD DS) em uma rede virtual do Azure, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama do cenário
Nesse cenário, os usuários externos precisam acessar os aplicativos que são executados ingressados no domínio. As VMs que executam os servidores de aplicativos e as VMs que executam os controladores de domínio são instaladas em seus próprios serviços de nuvem em uma rede virtual do Azure. Elas também estão incluídas em um conjunto de disponibilidade para melhorar a tolerância a falhas.

Floresta do ![Active Directory em máquinas virtuais na Rede Virtual do Azure ][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>Como isso difere do local?
Não há muita diferença entre instalar um controlador de domínio no Azure em comparação com um local. As diferenças principais estão listadas na tabela a seguir.

| Para configurar... | Configuração local | rede virtual do Azure |
| --- | --- | --- |
| **Endereço IP do controlador de domínio** |Atribuir um endereço IP estático nas propriedades do adaptador de rede |Execute o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático |
| **Resolvedor do cliente DNS** |Definir endereço de servidor DNS preferencial e alternativo nas propriedades do adaptador de rede de membros do domínio |Definir endereço de servidor DNS nas propriedades de rede virtual |
| **Armazenamento de banco de dados do Active Directory** |Como opção, altere o local de armazenamento padrão de C:\ |Você precisa alterar o local de armazenamento padrão de C:\ |

## <a name="create-an-azure-virtual-network"></a>Crie uma rede virtual do Azure
1. Entre no portal clássico do Azure.
2. Crie uma rede virtual. Clique em **Redes** > **Criar uma rede virtual**. Use os valores da tabela a seguir para concluir o assistente.

   | Nesta página do assistente… | Especifique esses valores |
   | --- | --- |
   |  **Detalhes de rede virtual** |<p>Nome: digite um nome para a sua rede virtual</p><p>Região: escolha a região mais próxima</p> |
   |  **DNS e VPN** |<p>Deixe o servidor DNS em branco</p><p>Não selecione a opção de VPN</p> |
   |  **Espaços de endereço da rede virtual** |<p>Nome da sub-rede: digite um nome para a sua sub-rede</p><p>IP Inicial: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Criar VMs para executar as funções de controlador de domínio e servidor DNS
Repita as etapas a seguir para criar VMs para hospedar a função de controlador de domínio, conforme necessário. Você deve implantar pelo menos dois controladores de domínio virtuais para fornecer redundância e tolerância à falhas. Se a rede virtual do Azure inclui pelo menos dois controladores de domínio configurados da mesma forma (ou seja, ambos são GCs, executam o servidor DNS e não contêm nenhuma função FSMO, etc.), coloque as VMs que executam tais controladores de domínio em um conjunto de disponibilidade para melhorar a tolerância.

Para criar as máquinas virtuais usando o Windows PowerShell em vez de interface do usuário, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. No portal clássico, clique em **Novo** > **Computação** > **Máquina Virtual** > **Da Galeria**. Use os valores a seguir para concluir o assistente. Aceite o valor padrão para uma configuração, a menos que outro valor seja sugerido ou necessário.

   | Nesta página do assistente… | Especifique esses valores |
   | --- | --- |
   |  **Escolha uma imagem** |Windows Server 2012 R2 Datacenter |
   |  **Configuração de máquina virtual** |<p>Nome da Máquina Virtual: digite um nome de rótulo único (como AzureDC1).</p><p>Novo nome de usuário: digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</p><p>Nova Senha/Confirmar: digite uma senha</p> |
   |  **Configuração de máquina virtual** |<p>Serviço de Nuvem: escolha <b>Criar um novo serviço de nuvem</b> para a primeira VM e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão a função de controlador de domínio.</p><p>Nome DNS do Serviço de Nuvem: especifique um nome global exclusivo</p><p>Região/Grupo de Afinidade/Rede Virtual: especifique o nome da rede virtual (como WestUSVNet).</p><p>Conta de Armazenamento: escolha <b>Usar uma conta de armazenamento gerada automaticamente</b> para a primeira VM e selecione esse nome de conta de armazenamento ao criar mais VMs que hospedarão a função de controlador de domínio.</p><p>Conjunto de Disponibilidade: escolha <b>Criar um conjunto de disponibilidade</b>.</p><p>Nome do conjunto de disponibilidade: digite um nome para o conjunto disponibilidade ao criar a primeira VM e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</p> |
   |  **Configuração de máquina virtual** |<p>Selecione <b>Instalar o Agente de VM</b> e quaisquer outras extensões que você precisa.</p> |
2. Anexe um disco a cada máquina virtual que executará a função de servidor de controlador de domínio. O disco adicional é necessário para armazenar o banco de dados, logs e SYSVOL do AD. Especifique um tamanho para o disco (por exemplo, 10 GB) e deixe a **Preferência de Cache do Host** definida como **Nenhum**. Consulte [Como anexar um disco de dados a uma máquina virtual Windows](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Depois de entrar na VM pela primeira vez, abra o **Gerenciador do Servidor** > **Serviços de Arquivo e Armazenamento** para criar um volume nesse disco usando o NTFS.
4. Reserve um endereço IP estático para VMs que executarão a função de controlador de domínio. Para reservar um endereço IP estático, baixe o Microsoft Web Platform Installer, [instale o PowerShell do Azure](/powershell/azure/overview) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

Para obter mais informações sobre como definir um endereço IP estático, consulte [Configurar um endereço IP interno estático para uma VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Instale o Windows Server Active Directory
Use a mesma rotina para [instalar o AD DS](https://technet.microsoft.com/library/jj574166.aspx) que você usa localmente (ou seja, você pode usar a interface do usuário, um arquivo de resposta ou o Windows PowerShell). Você precisa fornecer credenciais de administrador para instalar uma nova floresta. Para especificar o local do banco de dados, dos logs e do SYSVOL do Active Directory, altere o local de armazenamento padrão da unidade do sistema operacional pra o disco de dados adicional que você conectou à VM.

Após a instalação do DC, conecte-se novamente à VM e faça logon no DC. Não se esqueça de especificar credenciais de domínio.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Redefinir o servidor DNS para a rede virtual do Azure
1. Redefina a configuração do encaminhador DNS no novo servidor DNS/DC.
   1. No Gerenciador do Servidor, clique em **Ferramentas** > **DNS**.
   2. Em **Gerenciador DNS**, clique com o botão direito no nome do servidor DNS e clique em **Propriedades**.
   3. Na guia **Encaminhadores**, clique no endereço IP do encaminhador e clique em **Editar**.  Selecione o endereço IP e clique em **Excluir**.
   4. Clique em **OK** para fechar o editor e em **Ok** novamente para fechar as propriedades do servidor DNS.
2. Atualize a configuração do servidor DNS para a rede virtual.
   1. Clique em **Redes virtuais** > clique duas vezes na rede virtual que você criou > **Configurar** > **Servidores DNS**, digite o nome e o DIP de uma das VMs que executa a função de servidor DC/DNS e clique em **Salvar**.
   2. Selecione a VM e clique em **Reiniciar** para disparar a VM e definir as configurações de resolvedor DNS com o endereço IP do novo servidor DNS.

## <a name="create-vms-for-domain-members"></a>Criar VMs para membros do domínio
1. Repita as etapas a seguir para criar VMs para executar como servidores de aplicativos. Aceite o valor padrão para uma configuração, a menos que outro valor seja sugerido ou necessário.

   | Nesta página do assistente… | Especifique esses valores |
   | --- | --- |
   |  **Escolha uma imagem** |Windows Server 2012 R2 Datacenter |
   |  **Configuração de máquina virtual** |<p>Nome da Máquina Virtual: digite um nome de rótulo único (como AppServer1).</p><p>Novo nome de usuário: digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</p><p>Nova Senha/Confirmar: digite uma senha</p> |
   |  **Configuração de máquina virtual** |<p>Serviço de Nuvem: escolha **Criar um novo serviço de nuvem** para a primeira VM e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão o aplicativo.</p><p>Nome DNS do Serviço de Nuvem: especifique um nome global exclusivo</p><p>Região/Grupo de Afinidade/Rede Virtual: especifique o nome da rede virtual (como WestUSVNet).</p><p>Conta de Armazenamento: escolha **Usar uma conta de armazenamento gerada automaticamente** para a primeira VM e selecione esse nome de conta de armazenamento ao criar mais VMs que hospedarão o aplicativo.</p><p>Conjunto de Disponibilidade: escolha **Criar um conjunto de disponibilidade**.</p><p>Nome do conjunto de disponibilidade: digite um nome para o conjunto disponibilidade ao criar a primeira VM e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</p> |
   |  **Configuração de máquina virtual** |<p>Selecione <b>Instalar o Agente de VM</b> e quaisquer outras extensões que você precisa.</p> |
2. Após cada VM ter sido provisionada, conecte-se e a associe ao domínio. Em **Gerenciador de Servidores**, clique em **Servidor Local** > **WORKGROUP** > **Alterar...** e, em seguida, selecione **Domínio** e digite o nome do seu domínio local. Forneça as credenciais de um usuário de domínio e, em seguida, reinicie a VM para concluir o ingresso no domínio.

Para criar as máquinas virtuais usando o Windows PowerShell em vez de interface do usuário, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Para obter mais informações sobre como usar o Windows PowerShell, consulte [Introdução aos Cmdlets do Azure](/powershell/azure/overview) e [Referência de Cmdlets do Azure](/powershell/azure/get-started-azureps).

## <a name="see-also"></a>Consulte também
* [Como instalar uma nova floresta do Active Directory em uma rede virtual do Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Configurar uma VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Instalar uma Réplica do Controlador de Domínio do Active Directory em uma rede virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IaaS para profissionais de TI: (01) conceitos básicos de máquina virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Visão geral da Rede Virtual](../virtual-network/virtual-networks-overview.md)
* [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview)
* [PowerShell do Azure](/powershell/azure/overview)
* [Referência de Cmdlets do Azure](/powershell/azure/get-started-azureps)
* [Definir o endereço IP estático da VM do Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Como atribuir um IP estático para uma VM do Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [Instalar uma nova floresta do Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
* [Introdução à virtualização de Serviços do Domínio do Active Directory (AD DS) (nível 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
