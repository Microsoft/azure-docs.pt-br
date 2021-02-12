---
title: Personalizar as configurações de rede para uma VM de failover | Microsoft Docs
description: Fornece uma visão geral de personalizar as configurações de rede para uma VM de failover na replicação de VMs do Azure usando o Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: f63021275574e294fa372357d6e62724f5efe0f7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541183"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizar as configurações de rede da VM do Azure de destino

Este artigo fornece orientação sobre como personalizar as configurações de rede na VM (máquina virtual) do Azure de destino quando você estiver replicando e recuperando VMs do Azure de uma região para outra, usando [Azure site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como o Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Recursos de rede com suporte

Você pode fornecer as seguintes configurações de recurso-chave para a VM de failover ao replicar VMs do Azure:

- [Balanceador de carga interno](../load-balancer/load-balancer-overview.md)
- [IP público](../virtual-network/public-ip-addresses.md)
- [Grupo de segurança de rede](../virtual-network/manage-network-security-group.md) para a sub-rede e para a NIC

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de planejar suas configurações do lado de recuperação com antecedência.
- Crie os recursos de rede com antecedência. Forneça-o como uma entrada para que Azure Site Recovery serviço possa honrar essas configurações e garantir que a VM de failover obedeça a essas configurações.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Personalizar configurações de failover e de rede de failover de teste

1. Vá para **itens replicados**. 
2. Selecione a VM do Azure desejada.
3. Selecione **computação e rede** e selecione **Editar**. Observe que as definições de configuração da NIC incluem os recursos correspondentes na origem. 

     ![Personalizar as configurações de rede de failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selecione uma rede virtual de failover de teste.
5. Rede de failover é selecione **Editar** próximo à NIC que você deseja configurar. Na próxima folha que é aberta, selecione os recursos previamente criados correspondentes no failover de teste e no local de failover.

    ![Editar a configuração da NIC](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selecione **OK**.

Agora, Site Recovery honrará essas configurações e garantirá que a VM no failover esteja conectada ao recurso selecionado por meio da NIC correspondente.

Quando você disparar o failover de teste por meio do plano de recuperação, ele sempre perguntará a rede virtual do Azure. Esta rede virtual será usada para o failover de teste para os computadores que não têm as configurações de failover de teste pré-configuradas.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="unable-to-view-or-select-a-resource"></a>Não é possível exibir ou selecionar um recurso

Se você não puder selecionar ou exibir um recurso de rede, siga as seguintes verificações e condições:

- O campo de destino para um recurso de rede será habilitado somente se a VM de origem tiver uma entrada correspondente. Isso se baseia no princípio de que, para um cenário de recuperação de desastres, você desejaria a versão exata ou reduzida da sua fonte.
- Para cada recurso de rede, alguns filtros são aplicados na lista suspensa para garantir que a VM de failover possa se conectar ao recurso selecionado e a confiabilidade do failover seja mantida. Esses filtros são baseados nas mesmas condições de rede que teriam sido verificadas quando você configurou a VM de origem.

Validações do balanceador de carga interno:

- A assinatura e a região do balanceador de carga e a VM de destino devem ser iguais.
- A rede virtual associada ao balanceador de carga interno e a da VM de destino devem ser iguais.
- O SKU de IP público da VM de destino e o SKU do balanceador de carga interno devem ser os mesmos.
- Se a VM de destino estiver configurada para ser colocada em uma zona de disponibilidade, verifique se o balanceador de carga tem redundância de zona ou parte de qualquer zona de disponibilidade. (Os balanceadores de carga de SKU básicos não dão suporte a zonas e não serão mostrados na lista suspensa nesse caso.)
- Verifique se o balanceador de carga interno tem um pool de back-end criado previamente e uma configuração de front-end.

Endereço IP público:

- A assinatura e a região do IP público e a VM de destino devem ser iguais.
- O SKU de IP público da VM de destino e o SKU do balanceador de carga interno devem ser os mesmos.

Grupo de segurança de rede:
- A assinatura e a região do grupo de segurança de rede e a VM de destino devem ser iguais.


> [!WARNING]
> Se a VM de destino estiver associada a um conjunto de disponibilidade, você precisará associar o IP público e o balanceador de carga interno da mesma SKU com a do IP público e do balanceador de carga interno da VM no conjunto de disponibilidade. Caso contrário, o failover pode não ter sido executado.
