---
title: Configurar a preferência de roteamento para um endereço IP público - Portal do Azure
description: Saiba como criar um IP público com uma preferência de roteamento de tráfego da Internet
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672901"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Configurar a preferência de roteamento para um endereço IP público usando o portal do Azure

Este artigo mostra como configurar a [preferência de roteamento](./routing-preference-overview.md) por meio da rede de ISP (opção da **Internet** ) para um endereço IP público. Depois de criar o endereço IP público, você pode associá-lo aos seguintes recursos do Azure para os tráfegos de entrada e saída para a Internet:

* Máquina virtual
* Conjunto de escala de máquina virtual
* AKS (Serviço de Kubernetes do Azure)
* Balanceador de carga voltado para a Internet
* Gateway de Aplicativo
* Firewall do Azure

Por padrão, a preferência de roteamento para o endereço IP público é definida para a rede global da Microsoft para todos os serviços do Azure e pode ser associada a qualquer serviço do Azure.

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Criar um endereço IP público com uma preferência de roteamento
1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**.
3. Na caixa de pesquisa, digite *Endereço IP público*.
3. Nos resultados da pesquisa, selecione **Endereço IP público**. Em seguida, na página **Endereço IP público**, selecione **Criar**.
1. Para SKU, selecione **padrão**.
1. Para **preferência de roteamento**, selecione **Internet**.

      ![Criar um endereço IP público](./media/routing-preference-portal/public-ip-new.png)
1. Na seção **configuração de endereço IP IPv4** , insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **criar novo**, insira *RoutingPreferenceResourceGroup* e, em seguida, selecione **OK**. |
    | Location | Selecione **Leste dos EUA**.|
    | Zona de disponibilidade | Mantenha o valor padrão com **redundância de zona**. |
1. Selecione **Criar**.

    > [!NOTE]
    > Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. Contudo, a preferência de roteamento só dá suporte ao IPV4 no momento.

Você pode associar o endereço IP público criado acima a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use a seção CLI na página do tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP público à sua VM. Você também pode associar o endereço IP público criado acima a um [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração **front-end** do balanceador de carga. O endereço IP público serve como um endereço IP virtual de balanceamento de carga (VIP).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [IP público com a preferência de roteamento](routing-preference-overview.md).
- [Configure a preferência de roteamento para uma VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configure a preferência de roteamento para um endereço IP público usando o PowerShell](routing-preference-powershell.md).
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).