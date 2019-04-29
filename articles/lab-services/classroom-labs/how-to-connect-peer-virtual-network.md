---
title: Conectar a uma rede ponto a ponto nos serviços de laboratório do Azure | Microsoft Docs
description: Saiba como conectar sua rede de laboratório com outra rede como um par. Por exemplo, conectar-se a sua rede da escola/Universidade local com a rede virtual do laboratório no Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695337"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Conectar-se a rede do seu laboratório com uma rede virtual do par no Azure Lab Services 
Este artigo fornece informações sobre o emparelhamento de rede laboratórios com outra rede. 

## <a name="overview"></a>Visão geral
Emparelhamento de rede virtual permite que você se conecte redes virtuais do Azure. Uma vez emparelhadas, as redes virtuais aparecerão como uma para fins de conectividade. O tráfego entre máquinas virtuais em redes virtuais emparelhadas é roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre máquinas virtuais na mesma rede virtual, por meio de apenas endereços IP privados. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Talvez seja necessário conectar-se a rede do seu laboratório com uma rede virtual do par em alguns cenários, incluindo os seguintes:

- As máquinas virtuais no laboratório têm software que conecta-se a servidores de licença local para adquirir licença
- As máquinas virtuais no laboratório precisa de acesso a conjuntos de dados (ou quaisquer outros arquivos) em compartilhamentos de rede da Universidade. 

Certas redes locais conectadas à rede Virtual do Azure ou por meio [ExpressRoute](../../expressroute/expressroute-introduction.md) ou [Gateway de rede Virtual](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Esses serviços devem ser configurados fora do Azure Lab Services. Para saber mais sobre como conectar uma rede local ao Azure usando o ExpressRoute, consulte [Visão geral do ExpressRoute]) (... /expressroute/expressroute-Introduction.MD). Para conectividade local usar um Gateway de rede Virtual, o gateway especificado rede virtual e a conta de laboratório deve estar na mesma região.


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta de laboratório
Durante a criação de conta nova do laboratório, você pode escolher uma rede virtual existente que mostra a **rede virtual do par** lista suspensa. A rede virtual selecionada é connected(peered) para os laboratórios criados na conta de laboratório. Todas as máquinas virtuais em laboratórios que são criados após a criação dessa alteração teria acesso aos recursos na rede virtual emparelhada. 

![Selecione a rede virtual para o mesmo nível](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Para obter instruções passo a passo para a criação de uma conta de laboratório, consulte [configurar uma conta de laboratório](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Configurar depois que o laboratório for criado
A mesma propriedade pode ser habilitada no **configuração Labs** guia da **conta de laboratório** página se você não configurou uma rede ponto a ponto no momento da criação da conta de laboratório. Alterações feitas a essa configuração aplica-se somente para os laboratórios criados após a alteração.

![Habilitar ou desabilitar a rede virtual emparelhamento depois que o laboratório for criado](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Como você pode ver na imagem, você pode habilitar ou desabilitar **rede virtual do par** para laboratórios na conta de laboratório. 

> [!IMPORTANT]
> Essa alteração de configuração se aplica somente a laboratórios criados depois que a alteração for feita, não para os laboratórios existentes. 



## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)

