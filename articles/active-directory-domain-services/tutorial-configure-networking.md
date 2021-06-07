---
title: Tutorial – Configurar a rede virtual para o Azure AD Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a criar e configurar uma sub-rede da rede virtual do Azure ou um emparelhamento de rede para um domínio gerenciado do Azure Active Directory Domain Services usando o portal do Azure.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618409"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Configurar um emparelhamento de rede para um domínio gerenciado do Azure Active Directory Domain Services

Para fornecer conectividade a usuários e aplicativos, um domínio gerenciado do Azure AD DS (Azure Active Directory Domain Services) é implantado em uma sub-rede da rede virtual do Azure. Essa sub-rede da rede virtual só deve ser usada para os recursos do domínio gerenciado fornecidos pela plataforma Azure.

Quando você criar VMs e aplicativos próprios, eles não deverão ser implantados na mesma sub-rede da rede virtual. Em vez disso, você deverá criar e implantar seus aplicativos em uma sub-rede da rede virtual separada ou em uma rede virtual separada que esteja emparelhada com a rede virtual do Azure AD DS.

Este tutorial mostra como criar e configurar uma sub-rede da rede virtual dedicada ou como emparelhar outra rede na rede virtual do domínio gerenciado do Azure AD DS.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Entender as opções de conectividade de rede virtual para os recursos ingressados no domínio no Azure AD DS
> * Criar um intervalo de endereços IP e uma sub-rede adicional na rede virtual do Azure AD DS
> * Configurar um emparelhamento de rede virtual para uma rede separada do Azure AD DS

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para configurar o Azure AD DS.
* Você precisa de privilégios de *Colaborador* em sua assinatura do Azure para criar os recursos necessários do Azure AD DS.
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura um domínio gerenciado do Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você cria e configura o domínio gerenciado usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Opções de conectividade da carga de trabalho do aplicativo

No tutorial anterior, foi criado um domínio gerenciado que usava algumas opções de configuração padrão para a rede virtual. Essas opções padrão criaram uma rede virtual do Azure e uma sub-rede da rede virtual. Os controladores de domínio do Azure AD DS que fornecem os serviços de domínio gerenciado estão conectados a essa sub-rede da rede virtual.

Quando você cria e executa VMs que precisam usar o domínio gerenciado, a conectividade de rede precisa ser fornecida. Essa conectividade de rede pode ser fornecida de uma das seguintes maneiras:

* Criar uma sub-rede adicional da rede virtual na rede virtual do domínio gerenciado. Essa sub-rede adicional é o local em que você cria e conecta suas VMs.
    * Como as VMs fazem parte da mesma rede virtual, elas podem executar automaticamente a resolução de nomes e se comunicar com os controladores de domínio do Azure AD DS.
* Configurar o emparelhamento de rede virtual do Azure na rede virtual do domínio gerenciado para uma ou mais redes virtuais separadas. Essas redes virtuais separadas são o local em que você cria e conecta suas VMs.
    * Ao configurar o emparelhamento de rede virtual, você também precisa definir as configurações do DNS para usar a resolução de nomes novamente para os controladores de domínio do Azure AD DS.

Normalmente, apenas uma dessas opções de conectividade de rede é usada. Muitas vezes, a escolha depende de como você deseja gerenciar separadamente seus recursos do Azure.

* Caso deseje gerenciar o Azure AD DS e as VMs conectadas como um grupo de recursos, crie uma sub-rede da rede virtual adicional para as VMs.
* Caso deseje separar o gerenciamento do Azure AD DS e, em seguida, de qualquer VM conectada, use o emparelhamento de rede virtual.
    * Opte também por usar o emparelhamento de rede virtual para fornecer conectividade às VMs existentes no ambiente do Azure que estão conectadas a uma rede virtual existente.

Neste tutorial, você só precisará configurar uma dessas opções de conectividade de rede virtual.

Para obter mais informações sobre como planejar e configurar a rede virtual, confira as [considerações de rede para o Azure Active Directory Domain Services][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Criar uma sub-rede da rede virtual

Por padrão, a rede virtual do Azure criada com o domínio gerenciado contém uma única sub-rede da rede virtual. Essa sub-rede da rede virtual só deve ser usada pela plataforma Azure para fornecer serviços de domínio gerenciado. Para criar e usar suas próprias VMs nessa rede virtual do Azure, crie uma sub-rede adicional.

Para criar uma sub-rede da rede virtual para VMs e cargas de trabalho de aplicativo, conclua as seguintes etapas:

1. No portal do Azure, selecione o grupo de recursos do domínio gerenciado, como *myResourceGroup*. Na lista de recursos, escolha a rede virtual padrão, como *aadds-vnet*.
1. No menu à esquerda da janela da rede virtual, selecione **Espaço de endereço**. A rede virtual é criada com um só espaço de endereço *10.0.2.0/24*, que é usado pela sub-rede padrão.

    Adicione um intervalo de endereços IP adicional à rede virtual. O tamanho desse intervalo de endereços e do intervalo de endereços IP real a ser usado depende de outros recursos de rede já implantados. O intervalo de endereços IP não deve se sobrepor a nenhum intervalo de endereços existente no ambiente local ou do Azure. Dimensione o intervalo de endereços IP com um tamanho grande o suficiente para o número de VMs que espera implantar na sub-rede.

    No exemplo a seguir, um intervalo de endereços IP extra *10.0.3.0/24* é adicionado. Quando estiver pronto, selecione **Salvar**.

    ![Adicionar um intervalo de endereços IP de rede virtual adicional no portal do Azure](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Em seguida, no menu à esquerda da janela da rede virtual, selecione **Sub-redes** e, em seguida, escolha **+ Sub-rede** para adicionar uma sub-rede.
1. Insira um nome para a sub-rede, como *cargas de trabalho*. Se necessário, atualize o **Intervalo de endereços** caso deseje usar um subconjunto do intervalo de endereços IP configurado para a rede virtual nas etapas anteriores. Por enquanto, mantenha os padrões para opções como grupo de segurança de rede, tabela de rotas e pontos de extremidade de serviço.

    No seguinte exemplo, é criada uma sub-rede chamada *workloads* que usa o intervalo de endereços IP *10.0.3.0/24*:

    ![Adicionar uma sub-rede da rede virtual adicional no portal do Azure](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Quando estiver pronto, selecione **OK**. São necessários alguns instantes para a criação da sub-rede da rede virtual.

Ao criar uma VM que precisa usar o domínio gerenciado, selecione essa sub-rede da rede virtual. Não crie VMs na *aadds-subnet* padrão. Se você selecionar outra rede virtual, não haverá conectividade de rede nem resolução do DNS para acessar o domínio gerenciado, a menos que você configure o emparelhamento de rede virtual.

## <a name="configure-virtual-network-peering"></a>Configure emparelhamento de rede virtual

Talvez você já tenha uma rede virtual do Azure para VMs ou deseje manter a rede virtual de domínio gerenciado separada. Para usar o domínio gerenciado, as VMs de outras redes virtuais precisam ter uma maneira de se comunicar com os controladores de domínio do Azure AD DS. Essa conectividade pode ser fornecida usando o emparelhamento de rede virtual do Azure.

Com o emparelhamento de rede virtual do Azure, duas redes virtuais são conectadas, sem a necessidade de um dispositivo VPN (rede virtual privada). O emparelhamento de rede permite que você conecte rapidamente redes virtuais e defina fluxos de tráfego no ambiente do Azure.

Para obter mais informações sobre o emparelhamento, confira a [Visão geral do emparelhamento de rede virtual do Azure][peering-overview].

Para emparelhar uma rede virtual com a rede virtual de domínio gerenciado, conclua as seguintes etapas:

1. Escolha a rede virtual padrão criada para o domínio gerenciado chamado *aadds-vnet*.
1. No menu à esquerda da janela da rede virtual, selecione **Emparelhamentos**.
1. Para criar um emparelhamento, selecione **+ Adicionar**. No exemplo a seguir, a *aadds-vnet* padrão é emparelhada com uma rede virtual chamada *myVnet*. Defina as seguintes configurações com seus próprios valores:

    * **Nome do emparelhamento de aadds-vnet para a rede virtual remota**: Um identificador descritivo das duas redes, como *aadds-vnet-to-myvnet*
    * **Tipo de implantação da rede virtual**: *Resource Manager*
    * **Assinatura**: A assinatura da rede virtual com a qual deseja emparelhar, como *Azure*
    * **Rede virtual**: A rede virtual com a qual deseja emparelhar, como *myVnet*
    * **Nome do emparelhamento de myVnet para aadds-vnet**: Um identificador descritivo das duas redes, como *myvnet-to-aadds-vnet*

    ![Configurar o emparelhamento de rede virtual no portal do Azure](./media/tutorial-configure-networking/create-peering.png)

    Mantenha os outros padrões para o acesso à rede virtual ou o tráfego encaminhado, a menos que você tenha requisitos específicos para seu ambiente e, em seguida, selecione **OK**.

1. São necessários alguns instantes para a criação do emparelhamento na rede virtual do Azure AD DS e na rede virtual selecionada. Quando estiver pronto, o **Status de emparelhamento** relatará *Conectado*, conforme mostrado no seguinte exemplo:

    ![Redes emparelhadas conectadas com êxito no portal do Azure](./media/tutorial-configure-networking/connected-peering.png)

Antes que as VMs da rede virtual emparelhada possam usar o domínio gerenciado, configure os servidores DNS para permitir a resolução correta de nomes.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Configurar servidores DNS na rede virtual emparelhada

Para que as VMs e os aplicativos da rede virtual emparelhada se comuniquem com êxito com o domínio gerenciado, as configurações do DNS precisam ser atualizadas. Os endereços IP dos controladores de domínio do Azure AD DS precisam ser configurados como os servidores DNS na rede virtual emparelhada. Há duas maneiras de configurar os controladores de domínio como servidores DNS para a rede virtual emparelhada:

* Configurar os servidores DNS da rede virtual do Azure para usarem os controladores de domínio do Azure AD DS.
* Configurar o servidor DNS existente em uso na rede virtual emparelhada para usar o encaminhamento do DNS condicional para direcionar consultas para o domínio gerenciado. Essas etapas variam de acordo com o servidor DNS existente em uso.

Neste tutorial, vamos configurar os servidores DNS da rede virtual do Azure para direcionar todas as consultas para os controladores de domínio do Azure AD DS.

1. No portal do Azure, selecione o grupo de recursos da rede virtual emparelhada, como *myResourceGroup*. Na lista de recursos, escolha a rede virtual emparelhada, como *myVnet*.
1. No menu à esquerda da janela da rede virtual, selecione **Servidores DNS**.
1. Por padrão, uma rede virtual usa os servidores DNS internos fornecidos pelo Azure. Opte por usar servidores DNS **Personalizados**. Insira os endereços IP para os controladores de domínio do Azure AD DS, que geralmente são *10.0.2.4* e *10.0.2.5*. Confirme esses endereços IP na janela **Visão geral** do domínio gerenciado no portal.

    ![Configurar os servidores DNS da rede virtual para usarem os controladores de domínio do Azure AD DS](./media/tutorial-configure-networking/custom-dns.png)

1. Quando estiver pronto, selecione **Salvar**. São necessários alguns instantes para atualizar os servidores DNS para a rede virtual.
1. Para aplicar as configurações do DNS atualizadas às VMs, reinicie as VMs conectadas à rede virtual emparelhada.

Ao criar uma VM que precisa usar o domínio gerenciado, selecione essa rede virtual emparelhada. Se você selecionar outra rede virtual, não haverá conectividade de rede nem resolução do DNS para acessar o domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Entender as opções de conectividade de rede virtual para os recursos ingressados no domínio no Azure AD DS
> * Criar um intervalo de endereços IP e uma sub-rede adicional na rede virtual do Azure AD DS
> * Configurar um emparelhamento de rede virtual para uma rede separada do Azure AD DS

Para ver esse domínio gerenciado em ação, crie e una uma máquina virtual ao domínio.

> [!div class="nextstepaction"]
> [Una uma máquina virtual do Windows Server ao seu domínio gerenciado](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
