---
title: Solicitar um aumento nos limites de cota de vCPU por série de VMs do Azure
description: Como solicitar um aumento no limite de cota de vCPU para uma série de VMs no portal do Azure, o que aumenta o limite de vCPU regional total pelo mesmo valor.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745427"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Cota padrão: Aumentar limites por série de VM

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:

* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*.

A cota de vCPU padrão para instâncias de máquina virtual pagas conforme o uso e reservada é imposta em duas camadas para cada assinatura em cada região:

* A primeira camada é o *limite de vCPUs regional total*, em toda a série de VMs.
* A segunda camada é o *limite de vCPUs por VM da série*, como o vCPUs da série Dv3.

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para todas as instâncias de VM Spot não deverá exceder o limite de cota vCPU Spot aprovado. Se a cota de spot for excedida, a implantação da VM Spot não será permitida.

Você pode solicitar um aumento no limite de cota de vCPU para a série de VMs usando o portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPU regional total pelo mesmo valor.

Para saber mais sobre as cotas de vCPU padrão, consulte [cotas de vCPU de máquina virtual](../../virtual-machines/windows/quotas.md) e [limites de serviço e assinatura do Azure](./classic-deployment-model-quota-increase-requests.md).

Para saber mais sobre como aumentar o limite de vCPU por região para a cota padrão, consulte [cota padrão: aumentar os limites por região](regional-quota-requests.md).

Para saber mais sobre como aumentar os limites de vCPU de VM Spot, confira [cota de spot: aumentar os limites para todas as séries de VM](low-priority-quota.md).

Você pode solicitar um aumento nos limites de cota padrão do vCPU por série de VMs de uma das duas maneiras, conforme descrito nas seções a seguir.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Solicitar um aumento de cota padrão de ajuda + suporte

Para solicitar um aumento de cota de vCPU padrão por série de VMs de **ajuda + suporte**:

> [!NOTE]
> Você também pode solicitar um aumento de limite de cota para várias regiões por meio de um único caso de suporte. Para obter detalhes, consulte a etapa 8.

1. No menu do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**.

   ![Link de ajuda + suporte](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte**, selecione **Nova solicitação de suporte**.

    ![Criar uma solicitação de suporte](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **tipo de problema**, selecione **limites de serviço e de assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **assinatura**, selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma assinatura para uma cota maior](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para **tipo de cota**, selecione o **limite de assinaturas computação-VM (núcleos-vCPUs) aumenta**.

   ![Selecionar um tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Avançar: soluções** para abrir **detalhes do problema**. Selecione **fornecer detalhes** para inserir informações adicionais.

   ![Link "fornecer detalhes"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Nos **detalhes da cota**, execute as seguintes etapas:

   ![Detalhes adicionais de cota do TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para **modelo de implantação**, selecione o modelo apropriado.

   1. Para **locais**, selecione um local. Para o local selecionado, em **tipos**, em **selecionar um tipo**, escolha **padrão**.

      ![Detalhes da cota-tipos de cota](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções.

      Para obter mais informações sobre como aumentar os limites de cota Spot, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

   1. Em **padrão**, selecione a série de SKU para maiores cotas.

      ![Detalhes da cota-série de SKUs](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Insira os novos limites de cota que você deseja para esta assinatura. Para remover uma SKU da lista, desmarque a caixa de seleção ao lado da SKU ou selecione o ícone descartar "X".

      ![Selecione um novo limite de vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **locais** e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Especificar locais adicionais nos detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Solicitar um aumento de cota padrão de assinaturas

Para solicitar um aumento de cota de vCPU padrão por série de VMs das **assinaturas**:

> [!NOTE]
> Você também pode solicitar um aumento de limite de cota para várias regiões por meio de um único caso de suporte. Para obter detalhes, consulte a etapa 7.

1. No [portal do Azure](https://portal.azure.com), procure e selecione **assinaturas**.

   ![Assinaturas na pesquisa de portal do Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Assinaturas a serem selecionadas para alterações](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, selecione **uso + cotas**.

   ![O link "uso + cotas"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Selecione para aumentar a cota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Para **tipo de cota**, selecione o **limite de assinaturas computação-VM (núcleos-vCPUs) aumenta**.

   ![Selecionar um tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Nos **detalhes da cota**, execute as seguintes etapas:

   1. Para **modelo de implantação**, selecione o modelo apropriado e, para **locais**, selecione um local.

      ![Fornecer detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para o local selecionado, em **tipos**, selecione **selecionar um tipo** e, em seguida, selecione **padrão**.

      ![Selecionar tipo padrão](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções.

      Para obter mais informações sobre como aumentar os limites de cota Spot, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

   1. Para **padrão**, selecione a série de SKUs cujas cotas você deseja aumentar.

      ![Detalhes da cota-série de SKUs](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Insira os novos limites de cota que você deseja para esta assinatura. Para remover uma SKU da lista, desmarque a caixa de seleção ao lado da SKU ou selecione o ícone descartar "X".

      ![Selecione um novo limite de vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **locais** e, em seguida, selecione um tipo de VM apropriado.

   Esta etapa sobrecarrega a série de SKU que você selecionou para os locais anteriores. Insira os limites de cota que você deseja aplicar à série adicional.

   ![Selecionar locais adicionais nos detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.