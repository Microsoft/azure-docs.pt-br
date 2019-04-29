---
title: Solicitações de aumento de cota de vCPU do Azure Resource Manager | Microsoft Docs
description: Solicitações de aumento de cota de vCPU do Azure Resource Manager
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 204deaf3a67984c0dd5eca5352686719e7767885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649223"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Solicitações de aumento de cota de vCPU do Resource Manager

As cotas de vCPU do Resource Manager são aplicadas no nível da região e no nível de família de SKU.
Saiba mais sobre como as cotas são aplicadas na página [Limites de serviço da assinatura do Azure ](https://aka.ms/quotalimits).
Para saber mais sobre as famílias de SKU, compare custo e desempenho na página [Preços de máquinas virtuais](https://aka.ms/pricingcompute).

Para solicitar um aumento, siga as instruções abaixo para criar uma solicitação de suporte por meio da folha "Uso + cota" do Azure, disponível no Portal do Azure. 

## <a name="request-quota-increase-at-subscription-level"></a>Solicitar aumento de cota no nível da assinatura

1. Em https://portal.azure.com, selecione **Assinaturas**.

   ![Assinaturas](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a assinatura que precisa de uma cota maior.

   ![Escolha a assinatura](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecione **Uso + cotas**

   ![Selecione uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **Solicitar Aumento**.

   ![Solicitar Aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Etapa:1 – Selecione **Núcleos** como tipo de cota. 

   ![Preencher o formulário](./media/resource-manager-core-quotas-request/forms.png)
   
6. Etapa:2 – Selecione "Gerenciador de Recursos" como o modelo de implantação e selecione um local.

    ![Folha Problema de Cota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Selecione as Famílias de SKU que exigem um aumento.

    ![Série de SKU selecionada](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Insira os novos limites desejados na assinatura.

    ![Nova solicitação de cota do SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Para remover uma linha, desmarque o SKU no menu suspenso de família de SKU ou clique no ícone "x" de descarte.
Depois de inserir a cota desejada para cada família de SKU, clique em "Avançar" na página Etapa do problema para continuar com a criação da solicitação de suporte.

