---
title: Solicitações de aumento de cota de vCPU do Azure por VM | Microsoft Docs
description: solicitações de aumento de cota de vCPU por VM
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9caf82d2fa4683e845d23b5496f9e7b8f9682ec7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234837"
---
# <a name="vm-series-vcpu-limit-increase"></a>Aumento de limite de vCPU da série de VM

As cotas de vCPU do Resource Manager para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em duas camadas para cada assinatura, em cada região. 

A primeira camada é o **limite de VCPUs regional total** (em toda a série de VM), e a segunda camada é o **limite de vCPUs por série de VMs** (como a série D vCPUs). Sempre que uma nova VM for implantada, a soma do uso de vCPUs novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, a contagem de vCPU total e nova e existente implantada em toda a série de VMs não deve exceder a cota de vCPUs regional total aprovada para a assinatura. Se qualquer uma das cotas é excedida, a implantação de VM não será permitida.
Você pode solicitar um aumento do limite de cota de vCPUs para a série de VMs de portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPUs regional total pelo mesmo valor. 

Saiba mais sobre cotas na página de cotas do [vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e na página [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits) . 

Agora você pode solicitar um aumento via folha **ajuda + suporte** ou a folha **usos + cota** no Portal. 

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

7. No painel **detalhes da cota** , selecione modelo de implantação e selecione um local.

   ![Detalhes da cota DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Selecione as **famílias de SKU** que exigem um aumento. 

   ![Família de SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o SKU no menu suspenso de família de SKU ou clique no ícone "x" de descarte. Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

   ![Novos limites](./media/resource-manager-core-quotas-request/new-limits.png)


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
   
6. No painel **detalhes da cota** , selecione modelo de implantação e selecione um local.

   ![Folha Problema de Cota](./media/resource-manager-core-quotas-request/quota-details.png)

7. Selecione as **famílias de SKU** que exigem um aumento.

   ![Série de SKU selecionada](./media/resource-manager-core-quotas-request/sku-family.png)

8. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o SKU no menu suspenso de família de SKU ou clique no ícone "x" de descarte. Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** na página etapa do problema para continuar com a criação da solicitação de suporte.

   ![Nova solicitação de cota do SKU](./media/resource-manager-core-quotas-request/new-limits.png)
 
