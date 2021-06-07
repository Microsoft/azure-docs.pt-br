---
title: Conectar-se a uma rede par no Azure Lab Services | Microsoft Docs
description: Saiba como conectar sua rede de laboratório a outra rede como um par. Por exemplo, conecte sua rede de organização/universidade local com a rede virtual do laboratório no Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 99ea72f11c8f389a15171a1fe3b376646494903a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434236"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Conectar a rede do seu laboratório a uma rede virtual par no Azure Lab Services

Este artigo fornece informações sobre como emparelhar sua rede de laboratórios com outra rede.

## <a name="overview"></a>Visão geral

O emparelhamento de rede virtual permite que você conecte redes virtuais do Azure sem interrupção. Uma vez emparelhadas, as redes virtuais aparecerão como uma para fins de conectividade. O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas será roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre as máquinas virtuais na mesma rede virtual somente por meio de endereços IP privados. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).

Talvez você precise conectar a rede do laboratório a uma rede virtual par em alguns cenários, incluindo os seguintes:

- As máquinas virtuais no laboratório têm um software que se conecta a servidores de licença locais para adquirir licença.
- As máquinas virtuais no laboratório precisam acessar conjuntos de dados (ou outros arquivos) nos compartilhamentos de rede da universidade.

Determinadas redes locais são conectadas à rede virtual do Azure por meio do [ExpressRoute](../expressroute/expressroute-introduction.md) ou do [Gateway de Rede Virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md). Esses serviços devem ser configurados fora do Azure Lab Services. Para saber mais sobre como conectar uma rede local ao Azure usando o ExpressRoute, confira [Visão geral do ExpressRoute](../expressroute/expressroute-introduction.md). Para ter conectividade local usando um Gateway de Rede Virtual, o gateway, a rede virtual especificada e a conta de laboratório devem estar na mesma região.

> [!NOTE]
> Ao criar uma rede virtual do Azure que será emparelhada com uma conta de laboratório, é importante entender como a região da rede virtual afeta onde os laboratórios são criados.  Para obter mais informações, confira a seção do guia do administrador sobre [regiões/locais](./administrator-guide.md#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta de laboratório

Durante a criação da [nova conta de laboratório](tutorial-setup-lab-account.md), você pode escolher uma rede virtual existente mostrada na lista suspensa **Rede virtual par** na guia **Avançado**.  A lista só mostrará redes virtuais na mesma região que a conta do laboratório. A rede virtual selecionada está conectada (emparelhada) a laboratórios criados na conta de laboratório.  Todas as máquinas virtuais em laboratórios criados após essa alteração terão acesso aos recursos na rede virtual emparelhada.

![Selecionar VNet a ser emparelhada](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Intervalo de endereços

Também há uma opção para fornecer o **Intervalo de endereços** das máquinas virtuais para os laboratórios.  A propriedade **intervalo de endereços** só será aplicável se uma **rede virtual par** estiver habilitada para o laboratório. Se o intervalo de endereços for fornecido, todas as máquinas virtuais nos laboratórios na conta de laboratório serão criadas nesse intervalo de endereços. O intervalo de endereços deve estar na notação CIDR (por exemplo, 10.20.0.0/20) e não deve se sobrepor a nenhum intervalo de endereços existente.  Ao fornecer um intervalo de endereços, é importante pensar no número de *laboratórios* que será criado e fornecer um intervalo de endereços para acomodá-lo. Os Serviços de Laboratório pressupõem 512 máquinas virtuais por laboratório, no máximo.  Por exemplo, um intervalo de IP com “/23” pode criar apenas um laboratório.  Um intervalo com “/21” permitirá a criação de quatro laboratórios.

Se o **Intervalo de endereços** não estiver especificado, os Serviços de Laboratório usarão o intervalo de endereços padrão fornecido a ele pelo Azure ao criar a rede virtual a ser emparelhada com sua rede virtual.  O intervalo geralmente é algo como 10.x.0.0/16.  Isso pode levar a uma sobreposição de intervalos IP. Portanto, lembre-se de especificar um intervalo de endereços nas configurações do laboratório ou verifique o intervalo de endereços de sua rede virtual que está sendo emparelhada.

> [!NOTE]
> A criação de laboratórios poderá falhar se a conta de laboratório estiver emparelhada com uma rede virtual, mas tiver um intervalo de endereço IP muito limitado. Você poderá ficar sem espaço no intervalo de endereços se houver muitos laboratórios na conta de laboratório (cada laboratório usa 512 endereços). 
> 
> Se a criação de laboratório falhar, entre em contato com o proprietário/administrador da conta de laboratório e solicite o aumento do intervalo de endereços. O administrador pode aumentar o intervalo de endereços usando as etapas mencionadas na seção [Especificar um intervalo de endereços para VMs em uma conta de laboratório](#specify-an-address-range-for-vms-in-the-lab-account). 

## <a name="configure-after-the-lab-account-is-created"></a>Configurar após a criação da conta de laboratório

A mesma propriedade poderá ser habilitada na guia **Configuração dos laboratórios** da página **Conta de Laboratório** se você não tiver configurado uma rede par no momento da criação da conta do laboratório. As alterações feitas nessa configuração serão aplicáveis somente aos laboratórios criados após a alteração. Como você pode ver na imagem, você pode habilitar ou desabilitar a **Rede virtual par** para laboratórios na conta de laboratório.

![Habilitar ou desabilitar o emparelhamento VNet após a criação do laboratório](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Quando você seleciona uma rede virtual para o campo **Rede virtual par**, a opção **Permitir que o criador do laboratório escolha o local do laboratório** é desabilitada. Isso ocorre porque os laboratórios na conta de laboratório devem estar na mesma região que a conta de laboratório para que eles se conectem aos recursos na rede virtual par.

> [!IMPORTANT]
> A configuração de rede virtual emparelhada aplica-se somente a laboratórios criados após a alteração, e não a laboratórios existentes.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Especificar um intervalo de endereços para VMs na conta de laboratório
O procedimento a seguir tem etapas para especificar um intervalo de endereços para VMs no laboratório. Se você atualizar o intervalo especificado anteriormente, o intervalo de endereços modificados será aplicável somente às VMs criadas depois que a alteração tiver sido feita. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que você deve ter em mente. 

- O prefixo precisa ser menor ou igual a 23. 
- Se uma rede virtual for emparelhada com a conta de laboratório, o intervalo de endereços fornecido não poderá se sobrepor ao intervalo de endereços da rede virtual emparelhada.

1. Na página **Conta de Laboratório**, selecione **Configurações dos laboratórios** no menu à esquerda.
2. Para o campo **Intervalo de endereços**, especifique o intervalo de endereços para as VMs que serão criadas no laboratório. O intervalo de endereços deve estar na notação CIDR (Roteamento entre Domínios sem Classificação) (exemplo: 10.20.0.0/23). As máquinas virtuais do laboratório serão criadas nesse intervalo de endereços.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Configurar intervalo de endereços](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicionar um usuário como um proprietário do laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Definir outras configurações para um laboratório](how-to-configure-lab-accounts.md)