---
title: "Instalar uma réplica de controlador de domínio do Active Directory no Azure | Microsoft Docs"
description: "Um tutorial que explica como instalar um controlador de domínio por meio de uma floresta do Active Directory local em uma máquina virtual do Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: fb0bacac346445e6bde9df22f3355419e3162a3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalar uma Réplica do Controlador de Domínio do Active Directory em uma rede virtual do Azure
Este tópico mostra como instalar controladores de domínio adicionais (também conhecidos como controladores de domínio de réplica) para um domínio Active Directory local em máquinas virtuais (VMs) do Azure numa rede virtual do Azure.

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo.

Você também pode estar interessado nestes tópicos relacionados:

* Opcionalmente, você pode instalar uma nova floresta do Active Directory em uma rede virtual do Azure. Para essas etapas, consulte [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md).
* Para obter diretrizes conceituais sobre como instalar os Serviços de Domínio Active Directory (AD DS) em uma rede virtual do Azure, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama do cenário
Nesse cenário, os usuários externos precisam acessar os aplicativos que são executados ingressados no domínio. As VMs que executam os servidores de aplicativos e os controladores de domínio de réplica são instaladas em uma rede virtual do Azure. A rede virtual pode ser conectada à rede local por uma conexão [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md), conforme mostrado no diagrama a seguir, ou você pode usar o [ExpressRoute](../expressroute/expressroute-locations-providers.md) para uma conexão mais rápida.

Os servidores de aplicativos e os controladores de domínio são implantados em serviços de nuvem separados para distribuir o processamento de computação e em [conjuntos de disponibilidade](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para melhorar a tolerância a falhas.
Os DCs se replicam entre si e com os DCs locais por meio da replicação do Active Directory. Nenhuma ferramenta de sincronização é necessária.

![Diagrama de uma réplica de controlador de domínio do Active Directory em uma rede virtual no Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Crie um site do Active Directory para a rede virtual do Azure
É uma boa idéia criar um site no Active Directory que represente a região de rede correspondente à rede virtual. Que ajude a otimizar a autenticação, replicação e outras operações de localização do controlador de domínio. As etapas a seguir explicam como criar um site e, para obter mais informações, consulte [Adicionando um novo Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra Serviços e Sites do Active Directory: **Gerenciador do Servidor** > **Ferramentas** > **Serviços e Sites do Active Directory**.
2. Crie um site para representar a região onde você criou uma rede virtual do Azure: clique em **Sites** > **Ação** > **Novo site** > digite o nome do novo site, como Oeste dos EUA do Azure> selecione um link de site > **OK**.
3. Crie uma sub-rede e associe-a ao novo site: clique duas vezes em **Sites** > clique com o botão direito do mouse em **Sub-redes** > **Nova sub-rede** > digite o intervalo de endereços IP da rede virtual (como 10.1.0.0/16 no diagrama do cenário) > selecione o novo site do Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Crie uma rede virtual do Azure
1. No [portal clássico do Azure](https://manage.windowsazure.com), clique em **Novo** > **Serviços de Rede** > **Rede Virtual** >  **Criação Personalizada** e use os seguintes valores para concluir o assistente.

   | Nesta página do assistente… | Especifique esses valores |
   | --- | --- |
   |  **Detalhes de rede virtual** |<p>Nome: digite um nome para a rede virtual, como WestUSVNet.</p><p>Região: escolha a região mais próxima.</p> |
   |  **Conectividade DNS e VPN** |<p>Servidores DNS: especifique o nome e o endereço IP de um ou mais servidores DNS locais.</p><p>Conectividade: selecione **Configurar uma VPN site a site**.</p><p>Rede Loca: Especifique uma nova rede local.</p><p>Se você estiver usando o ExpressRoute em vez de uma VPN, confira [Configurar uma conexão do ExpressRoute por meio de um provedor do Exchange](../expressroute/expressroute-locations-providers.md).</p> |
   |  **Conectividade site a site** |<p>Nome: digite um nome para a rede local.</p><p>Endereço IP do dispositivo VPN: especifique o endereço IP público do dispositivo que se conectará à rede virtual. O dispositivo VPN não pode ser localizado por trás de um NAT.</p><p>Endereço: especifique os intervalos de endereços para sua rede local (como 192.168.0.0/16 no diagrama do cenário).</p> |
   |  **Espaços de endereço da rede virtual** |<p>Espaço de endereço: Especifique o intervalo de endereços IP para as máquinas virtuais que você deseja executar na rede virtual do Azure (como 10.1.0.0/16 no diagrama do cenário). Este intervalo de endereços não pode sobrepor os intervalos de endereços da rede local.</p><p>Sub-redes: especifique um nome e endereço de sub-rede para servidores de aplicativos (como Front-end, 10.1.1.0/24) e para os controladores de domínio (como Back-end, 10.1.2.0/24).</p><p>Clique em **adicionar Sub-rede de gateway**.</p> |
2. Em seguida, você configurará o gateway de rede virtual para criar uma conexão VPN site a site segura. Consulte [Configurar um Gateway de Rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para ver as instruções.
3. Crie a conexão VPN site a site entre a nova rede virtual e um dispositivo VPN local. Consulte [Configurar um Gateway de Rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para ver as instruções.

## <a name="create-azure-vms-for-the-dc-roles"></a>Crie máquinas virtuais do Azure para as funções de controlador de domínio
Repita as etapas a seguir para criar VMs para hospedar a função de controlador de domínio, conforme necessário. Você deve implantar pelo menos dois controladores de domínio virtuais para fornecer redundância e tolerância à falhas. Se a rede virtual do Azure inclui pelo menos dois controladores de domínio configurados da mesma forma (ou seja, ambos são GCs, executam o servidor DNS e não contêm nenhuma função FSMO, etc.), coloque as VMs que executam tais controladores de domínio em um conjunto de disponibilidade para melhorar a tolerância.
Para criar as máquinas virtuais usando o Windows PowerShell em vez de interface do usuário, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. No [Portal clássico do Azure](https://manage.windowsazure.com), clique em **Novo** > **Computação** > **Máquina Virtual** > **Da Galeria**. Use os valores a seguir para concluir o assistente. Aceite o valor padrão para uma configuração, a menos que outro valor seja sugerido ou necessário.

   | Nesta página do assistente… | Especifique esses valores |
   | --- | --- |
   |  **Escolha uma imagem** |Windows Server 2012 R2 Datacenter |
   |  **Configuração de máquina virtual** |<p>Nome da Máquina Virtual: digite um nome de rótulo único (como AzureDC1).</p><p>Novo nome de usuário: digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</p><p>Nova Senha/Confirmar: digite uma senha</p> |
   |  **Configuração de máquina virtual** |<p>Serviço de Nuvem: escolha <b>Criar um novo serviço de nuvem</b> para a primeira VM e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão a função de controlador de domínio.</p><p>Nome DNS do Serviço de Nuvem: especifique um nome global exclusivo</p><p>Região/Grupo de Afinidade/Rede Virtual: especifique o nome da rede virtual (como WestUSVNet).</p><p>Conta de Armazenamento: escolha <b>Usar uma conta de armazenamento gerada automaticamente</b> para a primeira VM e selecione esse nome de conta de armazenamento ao criar mais VMs que hospedarão a função de controlador de domínio.</p><p>Conjunto de Disponibilidade: escolha <b>Criar um conjunto de disponibilidade</b>.</p><p>Nome do conjunto de disponibilidade: digite um nome para o conjunto disponibilidade ao criar a primeira VM e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</p> |
   |  **Configuração de máquina virtual** |<p>Selecione <b>Instalar o Agente de VM</b> e quaisquer outras extensões que você precisa.</p> |
2. Anexe um disco a cada máquina virtual que executará a função de servidor de controlador de domínio. O disco adicional é necessário para armazenar o banco de dados, logs e SYSVOL do AD. Especifique um tamanho para o disco (por exemplo, 10 GB) e deixe a **Preferência de Cache do Host** definida como **Nenhum**. Consulte [Como anexar um disco de dados a uma máquina virtual Windows](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Depois de entrar na VM pela primeira vez, abra o **Gerenciador do Servidor** > **Serviços de Arquivo e Armazenamento** para criar um volume nesse disco usando o NTFS.
4. Reserve um endereço IP estático para VMs que executarão a função de controlador de domínio. Para reservar um endereço IP estático, baixe o Microsoft Web Platform Installer, [instale o PowerShell do Azure](/powershell/azure/overview) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Para obter mais informações sobre como definir um endereço IP estático, consulte [Configurar um endereço IP interno estático para uma VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instale o AD DS em máquinas virtuais do Azure
Conecte-se a uma VM e verifique se tem conectividade através da conexão VPN site a site ou ExpressRoute para recursos em sua rede local. Em seguida, instale o AD DS nas VMs do Azure. Você pode usar o mesmo processo que você usa para instalar um controlador de domínio adicional na sua rede local (interface do usuário, Windows PowerShell ou um arquivo de resposta). Enquanto instala o AD DS, certifique-se de especificar o novo volume para a localização do banco de dados, logs e SYSVOL do AD. Se você precisar de um atualizador na instalação do AD DS, consulte [Instalar os Active Directory Domain Services (nível 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [Instalar um Controlador de Domínio de Réplica do Windows Server 2012 em um Domínio Existente (nível 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigure o servidor DNS para a rede virtual
1. No [Portal do Azure](https://portal.azure.com), na caixa **Pesquisar recursos**, digite *Redes virtuais*, em seguida, clique em **Redes virtuais (clássicas)** nos resultados da pesquisa. Clique no nome da rede virtual e [reconfigure os endereços IP do servidor DNS para a sua rede virtual](../virtual-network/virtual-network-manage-network.md#dns-servers) e use os endereços IP estáticos atribuídos aos controladores de domínio da réplica em vez de endereços IP de servidores DNS locais.
2. Para garantir que todas as VMs do controlador de domínio de réplica na rede virtual sejam configuradas para usar servidores DNS na rede virtual, clique em **Máquinas Virtuais**, clique na coluna de status para cada VM e, em seguida, clique em **Reiniciar**. Aguarde até a VM mostrar o estado **Executando** antes de tentar conectar-se a ela.

## <a name="create-vms-for-application-servers"></a>Crie VMs para servidores de aplicativos
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

## <a name="additional-resources"></a>Recursos adicionais
* [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Como carregar controladores de domínio locais de Hyper-V existentes no Azure usando o Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md)
* [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IaaS para profissionais de TI: (01) conceitos básicos de máquina virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Cmdlets de gerenciamento do Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
