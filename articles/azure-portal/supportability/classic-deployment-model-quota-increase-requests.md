---
title: modelo de implantação clássico do Azure
description: O modelo de implantação clássico, agora substituído pelo modelo do Resource Manager, impõe um limite de cota de vCPU global para VMs e conjuntos de dimensionamento de máquinas virtuais.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745597"
---
# <a name="classic-deployment-model"></a>Modelo de implantação clássica

O modelo de implantação clássico é o modelo de implantação do Azure de geração mais antiga. Ele impõe um limite de cota de vCPU global para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. O modelo de implantação clássico não é mais recomendado e agora é substituído pelo modelo do Resource Manager.

Para saber mais sobre esses dois modelos de implantação e as vantagens de usar o Resource Manager, consulte [Resource Manager e implantação clássica](../../azure-resource-manager/management/deployment-models.md).

Quando uma nova assinatura é criada, uma cota padrão de vCPUs é atribuída a ela. Sempre que uma nova máquina virtual for implantada usando o modelo de implantação clássico, a soma do uso de vCPU novo e existente em todas as regiões não deverá exceder a cota vCPU aprovada para o modelo de implantação clássico.

Para saber mais sobre cotas, confira [assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Você pode solicitar um aumento no limite de cota de vCPU para o modelo de implantação clássico. Use **ajuda + suporte** ou **uso + cotas** no portal do Azure.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Solicitação por série de VM vCPU aumento de cota no nível da assinatura usando ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte usando **ajuda + suporte** no portal do Azure.

1. No menu do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**.

   ![Selecione ajuda + suporte na portal do Azure](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecione **Nova solicitação de suporte**.

   ![Criar uma nova solicitação de suporte no portal do Azure](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Em **tipo de problema**, escolha **limites de serviço e assinatura (cotas)**.

   ![Selecionar cotas como o tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione a assinatura para a qual aumentar uma cota](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para **tipo de cota**, selecione o **limite de assinaturas computação-VM (núcleos-vCPUs) aumenta**.

   ![Selecione o tipo de cota para aumentar](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Avançar: soluções** para abrir **detalhes do problema**. Selecione **fornecer detalhes** para fornecer informações adicionais.

   ![Forneça detalhes para ajudar sua solicitação](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes da cota**, selecione **clássico** e selecione um **local**.

   ![Adicionar detalhes, incluindo o modelo de implantação e o local](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Para a **família de SKUs**, selecione uma ou mais famílias de SKU para aumentar.

   ![Especificar a família de SKUs para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque a SKU da **família de SKU** ou selecione o ícone descartar "X". Depois de inserir uma cota para cada família de SKUs, selecione **salvar e continuar** em **detalhes da cota** para continuar com a solicitação de suporte.

   ![Solicitar novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Solicitação por série de VM vCPU aumento de cota no nível da assinatura usando uso + cotas

Siga as instruções abaixo para criar uma solicitação de suporte usando o **uso + cotas** no portal do Azure.

1. No [portal do Azure](https://portal.azure.com), procure e selecione **assinaturas**.

   ![Ir para assinaturas no portal do Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione a assinatura a ser modificada](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecione **uso + cotas**.

   ![Selecionar uso e cotas para uma assinatura](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Selecione para aumentar a cota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecione **computação – o limite de assinatura de VM (núcleos-vCPUs) aumenta** como o **tipo de cota**.

   ![Selecionar tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Avançar: soluções** para abrir **detalhes do problema**. Selecione **fornecer detalhes** para fornecer informações adicionais.

   ![Fornecer detalhes para sua solicitação](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes da cota**, selecione **clássico** e um **local**.

   ![Selecione os detalhes da cota, incluindo o modelo de implantação e o local](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecione uma ou mais famílias de SKU para um aumento.

   ![Selecione a família de SKUs para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque a SKU da **família de SKU** ou selecione o ícone descartar "X". Depois de inserir uma cota para cada família de SKUs, selecione **salvar e continuar** em **detalhes da cota** para continuar com a solicitação de suporte.

   ![Inserir nova cota](./media/resource-manager-core-quotas-request/new-limits-classic.png)

