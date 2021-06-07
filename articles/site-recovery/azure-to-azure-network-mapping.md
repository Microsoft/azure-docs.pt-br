---
title: Mapear redes virtuais entre duas regiões no Azure Site Recovery
description: Saiba mais sobre como mapear redes virtuais entre duas regiões do Azure para a recuperação de desastre de VM do Azure com o Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: ff1f80641dc3db1f6b69fc0223c60022f8cf8435
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95811640"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Configurar mapeamento de rede e endereçamento IP para VNets

Este artigo descreve como mapear duas instâncias de VNets (redes virtuais) do Azure localizadas em diferentes regiões do Azure e como configurar o endereçamento IP entre redes. O mapeamento de rede fornece um comportamento padrão para a seleção de rede de destino com base na rede de origem no momento da habilitação da replicação.

## <a name="prerequisites"></a>Pré-requisitos

Antes de mapear as redes, você deverá ter [VNets do Azure](../virtual-network/virtual-networks-overview.md) nas regiões de origem e destino do Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Configurar o mapeamento de rede manualmente (opcional)

Mapeie as redes da seguinte forma:

1. Na **Infraestrutura do Site Recovery**, clique em **+Mapeamento de Rede**.

    ![ Criar um mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Em **Adicionar mapeamento de rede**, selecione os locais de origem e destino. Em nosso exemplo, a VM de origem está executando na região do Leste da Ásia e é replicada para a região do Sudeste Asiático.

    ![Selecionar origem e destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Agora, crie um mapeamento de rede na direção oposta. Em nosso exemplo, a origem agora será o Sudeste Asiático e o destino será o Leste da Ásia.

    ![Adicionar painel de mapeamento de rede - Selecionar os locais de origem e destino para a rede de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapear redes ao habilitar a replicação

Se você não preparou o mapeamento de rede antes de configurar a recuperação de falhas para as VMs do Azure, poderá especificar uma rede de destino quando [configurar e habilitar a replicação](azure-to-azure-how-to-enable-replication.md). Ao fazer isso, acontecerá o seguinte:

- Baseado no destino que você selecionar, o Site Recovery criará automaticamente mapeamentos de rede da origem para a região de destino e do destino para a região de origem.
- Por padrão, o Site Recovery cria uma rede na região de destino que é idêntica à rede de origem. O Site Recovery adiciona **-asr** como um sufixo ao nome da rede de origem. É possível personalizar a rede de destino.
- Se o mapeamento de rede já tiver ocorrido para uma rede de origem, a rede de destino mapeada sempre será a padrão no momento da habilitação de replicações para mais VMs. Você pode optar por alterar a rede virtual de destino escolhendo outras opções disponíveis na lista suspensa. 
- Para alterar a rede virtual de destino padrão para novas replicações, você precisa modificar o mapeamento de rede existente.
- Se você quiser modificar um mapeamento de rede da região A para a região B, certifique-se de primeiro excluir o mapeamento de rede da região B para a região A. Após a exclusão de mapeamento reverso, modifique o mapeamento de rede da região A para a região B e crie o mapeamento inverso relevante.

>[!NOTE]
>* Modificar o mapeamento de rede altera apenas os padrões para novas replicações de VM. Ele não afeta as seleções de rede virtual de destino para as replicações existentes. 
>* Se você quiser modificar a rede de destino para uma replicação existente, vá para configurações de computação e rede do item replicado.

## <a name="specify-a-subnet"></a>Especificar uma sub-rede

A sub-rede da VM de destino é selecionada com base no nome da sub-rede da VM de origem.

- Se uma sub-rede com o mesmo nome da sub-rede da VM de origem estiver disponível na rede de destino, essa sub-rede será definida para a VM de destino.
- Se uma sub-rede com o mesmo nome não existir na rede de destino, a primeira sub-rede na ordem alfabética será definida como a sub-rede de destino.
- Você pode modificar a sub-rede de destino nas configurações de **computação e rede** da VM.

    ![Janela de propriedades de Computação e Rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Configurar endereçamento IP para VMs de destino

O endereço IP de cada NIC em uma máquina virtual de destino é configurado conforme a seguir:

- **DHCP**: se o NIC da VM de origem usar DHCP, o NIC da VM de destino também será definido para usar DHCP.
- **Endereço IP estático**: se o NIC da VM de origem usar endereçamento IP estático, o NIC da VM de destino também usará um endereço IP estático.


## <a name="ip-address-assignment-during-failover"></a>Atribuição de endereço IP durante failover

**Sub-redes de origem e destino** | **Detalhes**
--- | ---
Mesmo espaço de endereço | O endereço IP do NIC da VM de origem é definido como o endereço IP do NIC da VM de destino.<br/><br/> Se o endereço não estiver disponível, o próximo endereço IP disponível será definido como o destino.
Espaço de endereço diferente | O próximo endereço IP disponível na sub-rede de destino é definido como o endereço NIC da VM de destino.



## <a name="ip-address-assignment-during-test-failover"></a>Atribuição de endereço IP durante failover de teste

**Rede de destino** | **Detalhes**
--- | ---
Rede de destino é a VNet de failover | -O endereço IP de destino será estático com o mesmo endereço IP. <br/><br/>  -Se o mesmo endereço IP já estiver atribuído, o endereço IP será o próximo disponível no final do intervalo de sub-rede. Por exemplo: se o endereço IP de origem for 10.0.0.19 e a rede de failover usar o intervalo 10.0.0.0/24, o próximo endereço IP atribuído à VM de destino será 10.0.0.254.
A rede de destino não é a VNet de failover | -O endereço IP de destino será estático com o mesmo endereço IP.<br/><br/>  -Se o mesmo endereço IP já estiver atribuído, o endereço IP será o próximo disponível no final do intervalo de sub-rede.<br/><br/> Por exemplo: se o endereço IP estático de origem for 10.0.0.19 e o failover estiver em uma rede que não é a rede de failover, com o intervalo 10.0.0.0/24, o endereço IP estático de destino será 10.0.0.19 se disponível e, caso contrário, será 10.0.0.254.

- A rede virtual de failover é a rede de destino selecionada quando você configura a recuperação de desastre.
- É recomendável sempre usar uma rede de não produção para failover de teste.
- Você pode modificar o endereço IP de destino nas configurações **Computação e Rede** da VM.


## <a name="next-steps"></a>Próximas etapas

- Revise as [diretrizes de rede](./azure-to-azure-about-networking.md) para recuperação de desastre da VM do Azure.
- [Saiba mais](site-recovery-retain-ip-azure-vm-failover.md) sobre como reter endereços IP após failover.
