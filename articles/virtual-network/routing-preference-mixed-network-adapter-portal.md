---
title: Configurar ambas as opções de preferência de roteamento para uma máquina virtual-portal do Azure
description: Saiba como habilitar ambas as opções de preferência de roteamento
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
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679001"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Configurar ambas as opções de preferência de roteamento para uma máquina virtual

Este artigo mostra como configurar as duas opções de [preferência de roteamento](./routing-preference-overview.md) (Internet e Microsoft Global Network) para uma VM (máquina virtual). Isso é obtido usando duas interfaces de rede virtual, uma interface de rede com um IP público roteado por meio da rede global da Microsoft e a outra com um IP público roteado por meio de uma rede de ISP.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma máquina virtual com um endereço IP público seguindo as instruções descritas em [criar uma máquina virtual com um endereço IP público estático usando o portal do Azure](./virtual-network-deploy-static-pip-arm-portal.md) usando o portal do Azure. A escolha de preferência de roteamento padrão para o IP público é por meio da **rede global da Microsoft**. Depois que você tiver uma máquina virtual com um IP público criado, adicione um segundo IP público à VM que roteia o tráfego via rede de ISP de trânsito com sua preferência de roteamento configurada como **Internet**.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Criar um endereço IP público com uma preferência de roteamento escolha Internet
1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, digite *Endereço IP público*.
4. Nos resultados da pesquisa, selecione **Endereço IP público**. Em seguida, na página **Endereço IP público**, selecione **Criar**.
5. Nas opções **Preferência de roteamento**, selecione **Internet**.

      ![Criar um endereço IP público](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. Contudo, a preferência de roteamento só dá suporte ao IPV4 no momento.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Criar uma interface de rede e associar o IP público

1. Crie uma [interface de rede](./routing-preference-overview.md) usando o portal do Azure com as configurações padrão. 
1. [Associe o IP público à interface de rede](./associate-public-ip-address-vm.md) criada na seção anterior. Pode levar alguns segundos para que um endereço IP apareça. Exiba o endereço IP público atribuído à configuração de IP, conforme mostrado:

    ![Associar o IP público](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Anexar a interface de rede à VM

1. [Anexe a interface de rede criada na seção anterior à máquina virtual](./virtual-network-network-interface-vm.md).
2. Agora você pode exibir duas interfaces de rede virtual associadas à máquina virtual, uma com um IP público roteado por meio da rede global da Microsoft e a outra roteada via com uma rede de ISP, conforme mostrado:  ![ anexar um adaptador de rede a uma VM](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [IP público com a preferência de roteamento](routing-preference-overview.md).
- [Configure a preferência de roteamento para uma VM](tutorial-routing-preference-virtual-machine-portal.md).
- [Configure a preferência de roteamento para um endereço IP público usando o PowerShell](routing-preference-powershell.md).
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).