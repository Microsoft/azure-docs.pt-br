---
title: Identificar a cota da máquina virtual
description: Aumente os limites de cota para VMs pontuais, que fornecem um modelo de uso do Azure que permite que você assuma custos menores no Exchange para permitir que o Azure remova VMs conforme necessário.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745478"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Cota de spot: aumentar os limites para todas as séries de VM

As máquinas virtuais (VMs) spot fornecem um modelo diferente de uso do Azure. Eles permitem que você assuma custos menores no Exchange para permitir que o Azure remova as máquinas virtuais conforme necessário para implantações de instância de VM pagas conforme o uso ou reservadas. Para obter mais informações sobre VMs pontuais, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:

* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*.

Para o tipo de cota vCPU Spot, as cotas de vCPU do Resource Manager são impostas em toda a série de máquinas virtuais disponíveis como um único limite regional.

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para todas as instâncias de VM Spot não deverá exceder o limite de cota vCPU Spot aprovado. Se a cota de spot for excedida, a implantação da VM Spot não será permitida.

Este artigo discute como solicitar um aumento no limite de cota vCPU Spot usando o portal do Azure.

Para saber mais sobre as cotas de vCPU padrão, consulte [cotas de vCPU de máquina virtual](../../virtual-machines/windows/quotas.md) e [limites, cotas e restrições de serviço e assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Para saber mais sobre como aumentar o limite de vCPU por região, confira [cota padrão: aumentar os limites por região](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Solicitar um aumento de limite de cota de ajuda + suporte

Para solicitar um aumento de limite de cota de spot para todas as séries de máquina virtual usando **ajuda + suporte**:

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

1. Em **detalhes da cota**, execute as seguintes etapas:

   1. Para **modelo de implantação**, selecione o modelo apropriado e, para **locais**, selecione um local.

      ![Fornecer detalhes adicionais de cota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para o local selecionado, em **tipos**, em **selecionar um tipo**, escolha **Spot**.

      ![Selecionar tipo de spot](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções.

       Para obter mais informações, consulte [cota padrão: aumentar os limites por série de VMs](per-vm-quota-requests.md).

   1. Insira o novo limite de cota que você deseja para esta assinatura.

      ![Selecione uma nova cota para a VM Spot](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **locais** e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Especificar locais adicionais nos detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Solicitar um aumento de limite de cota no painel de assinaturas

Para solicitar um aumento de limite de cota de spot para todas as séries de VM do painel de **assinaturas** :

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

1. Selecione **Avançar: soluções** para abrir **detalhes do problema**. Selecione **fornecer detalhes** para inserir informações adicionais. Em **detalhes da cota**, insira as seguintes informações:

   1. Para **modelo de implantação**, selecione o modelo apropriado e, para **locais**, selecione um local.

      ![Fornecer detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para o local selecionado, em **tipos**, em **selecionar um tipo**, escolha **Spot**.

      ![Selecionar tipo de spot](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Para obter mais informações, consulte [cota padrão: aumentar os limites por série de VMs](per-vm-quota-requests.md).

   1. Insira o novo limite de cota que você deseja para esta assinatura.

      ![Insira um novo valor para o limite de vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **locais** e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Selecionar locais adicionais nos detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.
