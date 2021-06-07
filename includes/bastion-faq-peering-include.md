---
title: incluir arquivo
description: incluir arquivo
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94356631"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Ainda posso implantar vários hosts bastiões entre redes virtuais emparelhadas?

Sim. Por padrão, um usuário vê o host de bastiões que é implantado na mesma rede virtual na qual a VM reside. No entanto, no menu **conectar** , um usuário pode ver vários hosts bastiões detectados em redes emparelhadas. Eles podem selecionar o host de bastiões que preferem usar para se conectar à VM implantada na rede virtual.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Se meus VNets emparelhados forem implantados em assinaturas diferentes, a conectividade será realizada por meio do trabalho de bastiões?

Sim, a conectividade por meio de bastiões continuará funcionando para VNets emparelhadas em uma assinatura diferente para um único locatário. Não há suporte para assinaturas em dois locatários diferentes. Para ver a bastiões no menu suspenso **conectar** , o usuário deve selecionar as sub-rotinas às quais eles têm acesso na assinatura **> assinatura global**.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Filtro de assinaturas globais" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Tenho acesso à VNet emparelhada, mas não consigo ver a VM implantada lá.

Verifique se o usuário tem acesso de **leitura** à VM e à VNet emparelhada. Além disso, verifique em IAM que o usuário tem acesso de **leitura** aos seguintes recursos:

* A função de leitor na máquina virtual.
* A função de leitor na placa de interface de rede com endereço IP privado da máquina virtual.
* Função de leitor no recurso do Azure Bastion.
* Função de leitor na rede virtual (não é necessária se não houver uma rede virtual emparelhada).

|Permissões|Descrição|Tipo de permissão|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |Obtém um host bastião|Ação|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Obtém referências de Host Bastião em uma Rede Virtual.|Ação|
|Microsoft. Network/virtualNetworks/bastionHosts/default/Action|Obtém referências de Host Bastião em uma Rede Virtual.|Ação|
|Microsoft.Network/networkInterfaces/read|Obter uma definição de adaptador de rede.|Ação|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|Obtém uma definição de configuração de IP de interface de rede.|Ação|
|Microsoft.Network/virtualNetworks/read|Obter a definição de rede virtual|Ação|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|Obter referências a todas as máquinas virtuais em uma sub-rede de rede virtual|Ação|
|Microsoft.Network/virtualNetworks/virtualMachines/read|Obter referências a todas as máquinas virtuais em uma rede virtual|Ação|