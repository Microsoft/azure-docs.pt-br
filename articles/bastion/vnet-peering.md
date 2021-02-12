---
title: Emparelhamento VNet e arquitetura de bastiões do Azure
description: Neste artigo, saiba como o emparelhamento VNet e a bastiões do Azure podem ser usados juntos para se conectar às VMs.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 472261666c86b666efd09c7217d12e5a795a50d9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094873"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>Emparelhamento VNet e bastiões do Azure (visualização)

O emparelhamento de bastiões e VNet do Azure pode ser usado em conjunto. Quando o emparelhamento VNet é configurado, você não precisa implantar a bastiões do Azure em cada VNet emparelhada. Isso significa que, se você tiver um host de bastiões do Azure configurado em uma VNet (rede virtual), ele poderá ser usado para se conectar às VMs implantadas em uma VNet emparelhada sem implantar um host de bastiões adicional. Para obter mais informações sobre o emparelhamento VNet, consulte [sobre o emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md).

A bastiões do Azure funciona com os seguintes tipos de emparelhamento:

* **Emparelhamento de rede virtual:** Conectar redes virtuais na mesma região do Azure.
* **Emparelhamento de rede virtual global:** Conexão de redes virtuais em regiões do Azure.

## <a name="architecture"></a>Arquitetura

Quando o emparelhamento VNet é configurado, a bastiões do Azure pode ser implantada em topologias Hub-e-spoke ou malha completa. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual.

Depois de provisionar o serviço de bastiões do Azure em sua rede virtual, a experiência de RDP/SSH estará disponível para todas as suas VMs na mesma VNet, bem como VNets emparelhadas. Isso significa que você pode consolidar a implantação de bastiões em uma única VNet e ainda alcançar VMs implantadas em uma VNet emparelhada, centralizando a implantação geral.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagrama de design e arquitetura":::

Esta figura mostra a arquitetura de uma implantação de bastiões do Azure em um modelo Hub e spoke. Neste diagrama, você pode ver a seguinte configuração:

* O host de bastiões é implantado na rede virtual do Hub centralizado.
* O NSG (grupo de segurança de rede centralizado) é implantado.
* Um IP público não é necessário na VM do Azure.

**Tarefas**

1. Conecte-se ao portal do Azure usando qualquer navegador HTML5.
1. Selecione a máquina virtual à qual se conectar.
1. A bastiões do Azure é detectada diretamente na VNet emparelhada.
1. Com um único clique, a sessão RDP/SSH é aberta no navegador. Para limites de sessão simultânea de RDP e SSH, consulte [sessões de RDP e SSH](bastion-faq.md#limits).

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Connect":::

   Para obter mais informações sobre como se conectar a uma VM por meio da bastiões do Azure, consulte:

   * [Conecte-se a uma VM-RDP](bastion-connect-vm-rdp.md).
   * [Conectar-se a uma VM-SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas frequentes do Bastion](bastion-faq.md).