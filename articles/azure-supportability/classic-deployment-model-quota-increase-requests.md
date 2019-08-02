---
title: Modelo de implantação clássico do Azure | Microsoft Docs
description: modelo de implantação clássico do Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 70ad44eade871d52591014ee24e645b95c52f1e5
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234852"
---
# <a name="classic-deployment-model"></a>Modelo de implantação clássico

O modelo de implantação clássico é a geração mais antiga do modo de implantação do Azure que impõe um limite de cota de vCPU global para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. O modelo de implantação clássico não é mais recomendado e agora é substituído pelo modelo do Resource Manager. Para saber mais sobre esses dois modelos de implantação e a vantagem do Resource Manager, consulte a página modelo de implantação do Resource Manager. Quando uma nova assinatura é criada, uma cota padrão de vCPUs é atribuída a ela. Sempre que uma nova VM for implantada usando o modelo de implantação clássico, a soma do uso de vCPUs novo e existente em todas as regiões não deverá exceder a cota vCPU aprovada para o modelo de implantação clássico. Saiba mais sobre cotas na [página limites de serviço e assinatura do Azure](https://aka.ms/quotalimits)

Você pode solicitar um aumento no limite de vCPUs para o modelo de implantação clássico por meio da folha ajuda + suporte ou da folha usos + cota no Portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>A solicitação por série de VM vCPU aumento de cota no nível da assinatura usando a folha **ajuda + suporte**

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha ' ajuda + suporte ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **ajuda + suporte**.

   ![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Nova solicitação de suporte**. 

      ![Nova solicitação de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista suspensa tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

   ![Lista suspensa tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a assinatura que precisa de uma cota maior.

   ![Selecionar notícias de assinatura](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **computação – limite de assinatura de VM (núcleos-vCPUs) aumentos** no menu suspenso **tipo de cota** . 

   ![Selecionar tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Em **detalhes do problema**, forneça informações adicionais para ajudar a processar sua solicitação clicando em **fornecer detalhes**.

   ![Fornecer detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. No painel **detalhes da cota** , selecione clássico e selecione um local.

   ![Detalhes da cota DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selecione as **famílias de SKU** que exigem um aumento. 

   ![Família de SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o SKU no menu suspenso de família de SKU ou clique no ícone "x" de descarte. Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

   ![Novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Solicitação por série de VM vCPU aumento de cota no nível da assinatura usando **usos +** folha de cota

Siga as instruções abaixo usando para criar uma solicitação de suporte por meio da folha ' uso + cota ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **Assinaturas**.

   ![Assinaturas](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a assinatura que precisa de uma cota maior.

   ![Escolha a assinatura](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecione **Uso + cotas**

   ![Selecione uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **Solicitar Aumento**.

   ![Solicitar Aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecione **computação – o limite de assinatura de VM (núcleos-vCPUs) aumenta** como o tipo de cotação. 

   ![Preencher o formulário](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Em **detalhes do problema**, forneça informações adicionais para ajudar a processar sua solicitação clicando em **fornecer detalhes**.

   ![Fornecer detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. No painel **detalhes da cota** , selecione clássico e selecione um local.

   ![Detalhes da cota DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selecione as **famílias de SKU** que exigem um aumento. 

   ![Família de SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o SKU no menu suspenso de família de SKU ou clique no ícone "x" de descarte. Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

   ![Novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

